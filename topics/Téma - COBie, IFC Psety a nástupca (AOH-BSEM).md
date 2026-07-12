# Téma: COBie — štruktúra, vzťah k IFC Psetom, budúcnosť (AOH-BSEM)

Kontext: COBie sa v tomto projekte objavuje ako referenčný bod pri návrhu handover/exportných formátov pre AIMviewer. Táto téma zhŕňa, čo COBie skutočne je, ako sa vzťahuje k natívnym IFC Psetom, prečo je jeho budúcnosť otvorená, a čo ho má nahradiť.

## Čo COBie je (a čím nie je)

COBie (Construction Operations Building information exchange) je **americká národná špecifikácia** (pôvodne US Army Corps of Engineers 2007, spravovaná NIBS cez NBIMS-US), nie vlastný produkt buildingSMART International (bSI). Je definovaný ako Model View Definition (MVD) IFC schémy, ale **nie je** jedným z troch oficiálnych MVD pre IFC 4.3 — tie sú podľa `ifc43-docs.standards.buildingsmart.org` (sekcia Model View Definitions):

- Reference View (RV)
- Alignment Based View (AbV)
- Design Transfer View (DTV)

COBie sa v tomto zozname vôbec nenachádza. bSI ho historicky len certifikuje/podporuje ako "transitional activity", kým nevznikne medzinárodná ISO-based náhrada.

**Vývoj verzií:** COBie 2.26 (2009, IFC 2.3) → COBie 2.4 (2012, IFC4, spätne kompatibilný) → COBie 3.0 (2023, NBIMS-US v4, viazaný na IFC 4.3).

## Štruktúra

COBie je súbor tabuliek (Facility, Floor, Space, Zone, Type, Component, System, Job, Spare, Resource, Document, Contact, Attribute...), z ktorých každá má **fixné, preddefinované stĺpce** dané priamo štandardom (CamelCase konvencia, `Tabuľka.Pole` referencie). Formáty: SpreadsheetML/Excel (najpopulárnejší, ale len voliteľný), IFC/STEP, COBieLite XML, JSON (nové v3).

**Custom polia nie sú povolené** na existujúcich tabuľkách — pridanie vlastného stĺpca robí výstup technicky "iným" formátom, nie COBie (dôvod: CAFM/CMMS systémy majú pevné mapovanie na COBie stĺpce). Jediný sankcionovaný extension point je tabuľka **Attribute** — generický Name/Value/Unit mechanizmus naviazaný späť na ľubovoľný riadok. EIR/kontrakt teda len **scopuje** (required/optional/only-if-in-contract), nevymýšľa nové polia.

## Vzťah k štandardizovaným IFC Psetom

Kľúčové zistenie: väčšinu obsahu COBie Type/Component sheets **už pokrývajú natívne bSI Pset-y** v IFC 4.3 — nie je nutné nič "vymýšľať":

| COBie tabuľka/pole | Ekvivalentný IFC Pset |
|---|---|
| Type (manufacturer, model) | `Pset_ManufacturerTypeInformation` |
| Component (serial, install date) | `Pset_ManufacturerOccurrence` |
| Warranty | `Pset_Warranty` |
| Spare (čiastočne) | `Pset_RepairOccurrence` |
| Job (údržbové intervaly) | `Pset_MaintenanceStrategy`, `Pset_MaintenanceTriggerDuration/Condition/Performance` |
| — (COBie toto nemá vôbec) | `Pset_ServiceLife`, `Pset_Risk`, `Pset_Condition` |

Čo nemá priamy Pset ekvivalent (core-entity mechanizmus, nie property):
- COBie.Resource → `IfcLaborResource`, `IfcConstructionEquipmentResource` (Resource schéma, kap. 8)
- COBie.Job ako plný plán s kalendárom → `IfcTask` + `IfcWorkSchedule` + `IfcWorkCalendar` (Process Extension)
- COBie.Issue → doména BCF, nie IFC Pset

**Trade-off natívneho IFC prístupu:** aj pri výhradnom použití štandardizovaných Pset-ov väčšina komerčných CAFM/CMMS systémov (Planon, Archibus, FM:Systems, Maximo) nemá natívny import generických IFC Pset-ov — ich integrácia je postavená na COBie stĺpcoch, nie na prechádzaní IFC grafu. Native-Pset prístup dáva bohatší, presnejší dátový model (konzistentné s princípom "IFC ako sémantická vrstva" v tomto projekte), ale stráca plug-and-play import do legacy CAFM. Mitigácia: IFC/Pset dáta ako zdroj pravdy, COBie ako odvodený, "lossy" export generovaný na handover.

## Prečo je téma otvorená — kritika

Architektonická kritika (nima/buildingSMART UK&I): COBie je filtrovaná, **flattened** projekcia IFC dátového modelu pre jeden konkrétny use case (O&M). Keď sa nástroje stavajú na COBie namiesto na zdrojovom IFC, stavajú sa na konkrétnom výstupe, nie na bohatších zdrojových dátach. Odporúčanie znie: **vždy najprv IFC, nie COBie.** Toto priamo podporuje architektúru AIMviewer (IFC/ICDD ako zdroj pravdy, COBie len jeden z výstupných formátov).

## Budúcnosť: Asset Operations Handover (AOH-BSEM)

V roku 2020 začal bSI projekt na nahradenie COBie medzinárodným ISO-based štandardom. Po spore o vlastníctvo názvu (2021) sa už nebude volať "COBie" — oficiálny názov je **Asset Operations Handover – Building Systems Equipment Maintenance (AOH-BSEM)**, neformálne "Son of COBie". Vedie ho **Dr. Bill East** — pôvodný autor COBie z roku 2007 — takže nejde o konkurenčný projekt, ale o jeho vlastnú korekciu po 15 rokoch skúseností.

**Architektúra AOH frameworku (3 vrstvy):**
1. **"Core" objekty** — spoločné pre všetky domény: organizácie, parts, resources, tasks, documents, properties
2. **Doménovo-špecifické objekty** — building, road, rail, tunnel, bridge...
3. **Exchange requirements** — konkrétne pomenované výmeny naviazané na fázy životného cyklu

BSEM je prvá konkrétna aplikácia frameworku (equipment maintenance pre budovy) — priamy nástupca pôvodného COBie use case.

**Kľúčové technické rozdiely oproti COBie:**
- Implementácia cez nové bSI nástroje **IDS (Information Delivery Specification) + Use Case Management**, nie klasické mvdXML/MVD.
- BSEM dátové sety **nesmú byť naviazané na geometriu** (na rozdiel od starého Basic FM Handover MVD, ktorý dovoľoval dáta "overloadnuť" priamo na geometrický IFC model).
- Rozsah je **explicitne uzavretý zoznam** IFC objektov — pridávanie ďalších je zakázané špecifikáciou.
- BSEM = výhradne manufactured equipment s model number a warranty vyžadujúce maintenance; **flow devices (duct, pipe) explicitne vylúčené**.
- Opravuje zdokumentovanú limitáciu starého MVD: Parts/Job Plans boli obmedzené len na Product Types, nedalo sa korektne reprezentovať napr. dodávku náhradných stropných dlaždíc alebo spotrebný materiál bez formálneho Product Type.

**Publikované dokumenty (2022):** BSEM IDM Part 1 (Business Process), Part 2 (Technical Requirements — mapovanie na IFC 4.3), Part 3 (Implementation Guide + Example Files).

## Ako sme na to prišli

Otázka vznikla z praktickej úvahy o AIMviewer handover exporte — najprv overenie, či COBie vôbec je oficiálne IFC 4.3 MVD (nie je). To viedlo k otázke, čo presne COBie je a ako funguje, následne k otázke, či sa dá nahradiť vlastnými Psetmi (áno, z veľkej časti, s CAFM trade-offom), a napokon k otázke o budúcnosti COBie samotného. Ukázalo sa, že bSI má od roku 2020 živý nástupnícky projekt (AOH-BSEM), vedený pôvodným autorom COBie — čo mení otázku z "má zmysel sa učiť COBie" na "oplatí sa sledovať AOH-BSEM ako budúci cieľový formát namiesto COBie".

## Aktuálny stav (k júlu 2026)

Podľa najaktuálnejších dostupných zdrojov (apríl 2025, Construction Management/CADuser, citujúc Vitalija Tetervova z yBIM) je projekt stále "live", ale s **neistým financovaním** ("received a lot of interest, yet just a handful committed financially"). Nie je potvrdená finálna ratifikácia ako ISO/bSI štandard. Paralelne beží NIBS COBie 3.0 (public review 2023) — nejasné, či UK/medzinárodný trh prejde na AOH-BSEM alebo zostane pri COBie 3.

**Dôležité pre diplomovku:** nenašiel sa žiadny dedikovaný peer-reviewed akademický článok o AOH-BSEM — existujúca akademická literatúra o IFC/FM handover sa odkazuje výhradne na klasický COBie. Je to potenciálna medzera na citovanie/kontextualizáciu.

## Zdroje

**Primárne (najautoritatívnejšie):**
- `ifc43-docs.standards.buildingsmart.org` — Introduction, sekcia Model View Definitions (oficiálne MVD pre IFC 4.3)
- NIBS — `nibs.org/nbims/v3/cobie` (COBie v3 špecifikácia, dátové polia)
- Bill East, LinkedIn Pulse — "The First Piece of the Puzzle" (2022) — priamo od autora AOH-BSEM
- buildingSMART International — AOH-BSEM IDM Part 1/2/3 (2022)

**Sekundárne / implementačné (nie schema autorita, len kontext):**
- Construction Management — "What's the future of COBie?" (apríl 2025, D. Chadwick, cituje V. Tetervova)
- CADuser — "Whither COBie?" (2023)
- Vitalij Tetervov, LinkedIn Pulse — "COBie 2.5? COBie 3? AOH-BSEM?"

## Súvisiace

- [[Glossary]] — *COBie*, *Asset Operations Handover*, *Information Delivery Manual (IDM)*, *Handover*
- [[IfcSystem]]
- [[IfcSpace]]
