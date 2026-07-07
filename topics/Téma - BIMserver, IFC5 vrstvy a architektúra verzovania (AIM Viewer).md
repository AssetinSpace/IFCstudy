# Téma: BIMserver, IFC5 layers a composition/versioning architektúra pre AIM Viewer

Kontext: Otázka, ako riešiť verzovanie a merging viacerých zdrojov/revízií IFC dát v [[AIMviewer]], bola preskúmaná naprieč tromi referenčnými bodmi — historický object-model server (BIMserver), budúca schéma (IFC5) a princípy z iných odvetví (MDM, semantic web, event sourcing). Záver priamo nadväzuje na [[Téma - Cross-file spatial containment (ETL, IDS, ICDD)]] — tam riešený problém federácie naprieč súbormi je špeciálny prípad všeobecnejšieho problému riešeného tu: viacero nezávislých zdrojov dát nad tým istým asset-om.

## 1. BIMserver — historický referenčný bod (object-model persistency)

BIMserver (TU Eindhoven/TNO, 2010, Beetz & van Berlo; `opensourceBIM/BIMserver`, AGPL-3.0, stále udržiavaný) neukladá IFC ako súbory, ale ako objekty v Berkeley DB (key-value store).

**Princíp verzovania (prenositeľný do AIM Viewer):**
- Zložený kľúč: `package/class + Project ID (pid) + Object ID (oid) + Revision ID (rid, záporné číslo)`
- Hierarchia `Project → Revision → ConcreteRevision`
- Append-only — nič sa neprepisuje in-place, každý checkin je nová revízia

**Princíp mergingu (poučenie z toho, čo sa nepodarilo):**
- Merge = federácia formou union setu naprieč sub-projektmi, nie diff-based 3-way merge
- Vlastné priznanie maintainerov: algoritmus model merge negarantuje validný IFC výstup
- Neriešené roky: duplicitné GUID a delete-tracking (GitHub issue #449, otvorené od 2016)

**Poučenie pre AIM Viewer:** neopakovať naivný union prístup bez identity-matchingu a tombstone mechanizmu.

Alternatívy, ktoré tento problém riešia lepšie (implementačné príklady, nie schema autorita):
- `ifcmerge` (brunopostle) — skutočný 3-way merge, vyžaduje stabilné STEP ID (nevhodné pre nezávislé re-exporty z Revitu)
- `AtomIfc` (Qonic, MIT) — granulárne IFC "atómy" s ownerHistory logom (added/changed/removed)

## 2. IFC5 — layer-based composition (prevzaté z USD)

Podľa Léona van Berla (buildingSMART, `buildingSMART/IFC5-development/Examples_FAQ.md`): viacero prispievateľov dodáva časti dát do spoločného datasetu. Vrstvy (layers) sa **skladajú (compose) za behu**, nenatrvalo — princíp "strongest opinion wins", rovnaký ako v Pixar USD. Architektúra je ECS (Entity-Component-System) — entita je len ID, komponenty (napr. FireRating) sa pripájajú z rôznych zdrojov nezávisle, bez zásahu do pôvodných dát.

## 3. Prečo IFC4.3 nejde jednoducho "upgradovať" na layers

Ide o architektonický nesúlad medzi STEP/EXPRESS a composition modelom, nie o chýbajúcu funkciu:

| | IFC4.3 (STEP/EXPRESS) | IFC5 (ECS/layers) |
|---|---|---|
| Kódovanie | Pozičné, ordered hodnoty | Sparse, pomenované komponenty |
| Kompletnosť | Mandatory atribúty, celá entita naraz | Čiastočné definície, doplnené inde |
| Vzťahy | Odkazy na čísla riadkov v tom istom súbore | Cross-file, cez stabilné ID |
| Status | Ratifikovaný ISO 16739-1:2024 | Vo vývoji, JSON-based |

**JSON je nutná, nie postačujúca podmienka** — ifcJSON existuje už dnes ako alternatívna serializácia IFC4.x, ale bez zmeny schémy (ECS + composition pravidlá) zostáva rovnaký monolitický model. Pre porovnanie: USD ani nepoužíva JSON ako primárny formát (`.usda`/`.usdc`) — composition model je nezávislý od serializácie.

## 4. Čo IFC schéma o probléme skutočne hovorí (normatívne overené)

Overené priamo v `ifc43-docs.standards.buildingsmart.org`, entita [[IfcRoot]] — EXPRESS definícia potvrdzuje `GlobalId : IfcGloballyUniqueId` ako mandatory atribút s `UNIQUE UR1 : GlobalId`, zatiaľ čo `OwnerHistory : OPTIONAL IfcOwnerHistory` je od IFC4 explicitne OPTIONAL (IFC4-CHANGE poznámka v spec to potvrdzuje). Dokumentácia sama upozorňuje (NOTE k IfcRoot), že view definitions a implementation agreements môžu na použitie `OwnerHistory` pre object versioning uvaliť dodatočné obmedzenia — spec teda výslovne nepredpisuje, ako sa má history/versioning riešiť.

- **GlobalId** — 128-bit GUID, base64 na 22 znakov, spoľahlivý match key naprieč revíziami aj súbormi (viď aj [[Téma - Cross-file spatial containment (ETL, IDS, ICDD)]] — tam rovnaká vlastnosť rieši federáciu, tu rieši identity-matching pri verzovaní)
- **[[IfcOwnerHistory]]** — len jeden snapshot "kto naposledy zmenil", nie plný revision log; od IFC4 OPTIONAL
- **Diskusia v `buildingSMART/NextGen-IFC` (issue #16)** smeruje k deprecovaniu `IfcOwnerHistory` úplne — komunitný konsenzus: provenance dát by sa nemala ukladať vo formáte na výmenu dát, ale v CDE alebo internom manažment systéme
- **Dôsledok:** IFC štandard vedome necháva zodpovednosť za históriu/verzovanie na prijímajúcom systéme — presne na architektúre typu AIM Viewer. Toto nie je medzera v spec, je to zámerné rozhranie zodpovednosti.

## Ako sme na to prišli

Pôvodná otázka bola praktická — ako v AIM Vieweri riešiť viacero revízií/zdrojov nad tým istým modelom bez straty histórie. Prvý referenčný bod, BIMserver, ukázal funkčný append-only princíp verzovania, ale jeho merging (naivný union) sa ukázal ako varovný príklad — roky neriešené duplicitné GUID a delete-tracking potvrdili, že federácia bez identity-matchingu a tombstone mechanizmu nefunguje. Druhý referenčný bod, IFC5 layers/composition, priniesol koncepčne čistejšie riešenie ("strongest opinion wins"), no porovnanie STEP/EXPRESS vs. ECS ukázalo, že ide o štrukturálny rozdiel v kódovaní a vzťahoch, nie o vec, ktorú by vyriešila len zmena serializácie na JSON. Overenie priamo v spec (`IfcRoot`, `OwnerHistory` OPTIONAL od IFC4, diskusia o jeho deprecovaní) potvrdilo, že IFC normatívne necháva verzovanie zámerne mimo seba — čo legitimizuje riešenie na úrovni vlastnej databázovej vrstvy, nie hack okolo schémy. Z toho vyplynul návrh bitemporálneho fact-table modelu (SCD Type 2 / event sourcing) ako SQL implementácie toho istého princípu, aký IFC5 rieši na úrovni schémy.

## 5. Navrhovaná architektúra pre AIM Viewer

Bitemporálny fact-table model (SCD Type 2 / event-sourcing pattern):

```
object_facts
├── source_id, revision_id (append-only)
├── global_id (IFC GUID — primary match key)
├── property_key, property_value
├── valid_from, valid_to (NULL = platné; NIKDY hard delete)

source_priority
├── property_namespace
└── priority_source_id  (survivorship rule)

decisions  (dôvod zmeny — pozri bod 7)
├── decision_id, description, category, source_reference, decided_by
```

**Composed view** (aktuálny stav) = SQL view aplikujúci `source_priority` na najnovšie platné fakty — SQL implementácia toho istého princípu ako IFC5 "strongest opinion wins".

**Identity resolution:** `GlobalId` ako primárny kľúč, fallback heuristika (typ + poloha + názov) pre nezhody spôsobené nestabilitou GUID pri Revit re-exportoch. Vlastný interný UUID oddelený od `GlobalId` — rieši edge cases pri duplicite/zmene GUID.

## 6. Paralely v iných odvetviach

| Odvetvie | Termín | Ekvivalent v AIM Viewer |
|---|---|---|
| MDM (podnikové dáta) | Golden record, survivorship rules, entity resolution | `source_priority`, identity matching |
| Semantic Web | `owl:sameAs`, PROV-O ontológia | `GlobalId` matching, decision/provenance tabuľka |
| Softvérová architektúra | Event Sourcing / CQRS | `object_facts` (append-only log), composed view (projection) |
| Distribuované systémy | CRDT (Conflict-free Replicated Data Types) | formálny základ pre "strongest opinion wins" |
| GIS/kartografia | Conflation | identity matching cez geometriu + atribúty |

**UK Golden Thread ≠ Golden Record** — odlišné pojmy, netreba zamieňať:
- *Golden record* (MDM) = technika/výsledok zlúčenia záznamov
- *Golden thread* (UK Building Safety Act 2022, s. 88) = legálna povinnosť: nepretržitý, dôveryhodný, immutable audit trail pre Higher-Risk Buildings — shareability, attributability, immutable historical states
- Súvis: navrhovaná bitemporálna architektúra je technické riešenie, ktoré by golden thread požiadavky reálne spĺňalo (nie len compliance checkbox)

## 7. "Prečo sa to zmenilo" — najvyššia dlhodobá hodnota

Kto/kedy sa dá zachytiť pasívne (napr. cez `OwnerHistory`, ak by sa použil), "prečo" vyžaduje aktívny ľudský vstup — preto to takmer žiadny CAFM/CDE systém nerobí poriadne.

**Precedenty (implementačné/procesné, nie IFC schema):**
- Architecture Decision Record (ADR) — softvérové inžinierstvo
- Engineering Change Order/Notice (ECO/ECN) — PLM v leteckom/farmaceutickom priemysle, regulačne vynútené

**Návrh:** samostatná `decisions` tabuľka (many-to-one voči `object_facts`), s kategorizáciou (client_change, code_compliance, clash_resolution, error_correction...) a odkazom na zdrojový dokument (RFI, email, zápisnica). Naviazanie na úroveň revízie/importu (nie nutne per-element) kvôli friction pri zbere. LLM-asistované navrhovanie prepojenia dokument→zmena ako spôsob zníženia friction.

## Súvisiace

- [[Téma - Cross-file spatial containment (ETL, IDS, ICDD)]]
- [[IfcSystem]]
- [[IfcZone]]
- [[Glossary]] — `GUID`, `Federovaný model`
