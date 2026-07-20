# Téma: Väzba IFC prvku na normu/vyhlášku/predpis — mechanizmy a voľba pre AIMviewer

Kontext: Otázka vznikla z potreby AIMvieweru — kliknúť na prvok a vidieť štruktúrovane zapísané to, čo dnes stojí len v technickej správe: "tento prvok podlieha požiadavkám vyhlášky X". Podmienka: **žiadny odkaz na konkrétny dokument/verziu**, keďže vyhláška/norma sa môže časom meniť. Overovanie súladu (IDS a pod.) bolo v tejto téme explicitne mimo scope — cieľom bol len **zápis**, nie validácia.

## Skúmané mechanizmy

IFC 4.3 ponúka tri odlišné, navzájom nekonkurujúce si cesty:

| Mechanizmus | Entita | Väzba na verziu dokumentu? | IDS-checkovateľné? |
|---|---|---|---|
| Kvalitatívny constraint/objective | [[IfcObjective]] (subtyp [[IfcConstraint]]) | Nie — `ConstraintSource` je voľný text | Nie |
| Kvantitatívna metrika | [[IfcMetric]] (subtyp `IfcConstraint`) | Nie | Nie |
| Klasifikácia | [[IfcClassification]] / `IfcClassificationReference` | Nie — `Location`/`Edition` voliteľné | **Áno** (Classification facet) |
| Dokumentová referencia | [[IfcDocumentReference]] / `IfcDocumentInformation` | **Áno** — `Location` = URI konkrétneho súboru | Nie (žiadny Document facet) |

## Prečo je to tak (zdôvodnenie)

`IfcObjective` je sémanticky najpresnejšia zhoda pre formuláciu "prvok podlieha požiadavke X" — `IfcObjectiveEnum` má priamo hodnoty `CODECOMPLIANCE`, `REQUIREMENT`, `SPECIFICATION`, teda schéma s týmto use case explicitne počíta. Kľúčový detail je, že `IfcConstraint.ConstraintSource` je typu `IfcLabel` (voľný text), nie `IfcDocumentReference` ani URI — dá sa doň teda napísať `"Vyhláška č. 406/2023 Z.z."` bez toho, aby sa väzba rozbila pri novelizácii.

`IfcClassification` funguje analogicky (viem zapísať normu ako klasifikačný systém bez `Location`/`Edition`), ale sémanticky je určený na *kategorizáciu*, nie na vyjadrenie požiadavky — presne to je dôvod, prečo ho projekt už používa pre SNIM/CCI. Dôležitý rozdiel oproti `IfcObjective`: IDS 1.0 (overené na `github.com/buildingSMART/IDS` dokumentácii) má presne 6 facetov — Entity, Attribute, **Classification**, Property, Material, PartOf. Facet pre Constraint/Objective/Document neexistuje. To znamená, že ak by sa v budúcnosti predsa len chcelo automatizované overovanie ("má tento prvok priradenú klasifikáciu zodpovedajúcu norme X"), musela by byť väzba postavená na `IfcClassification`, nie na `IfcObjective` — bez ohľadu na to, že `IfcObjective` je sémanticky presnejší pre samotné *vyjadrenie* požiadavky.

`IfcDocumentReference` bol vylúčený ako primárny nosič práve kvôli explicitnej podmienke zo zadania — `Location` je URI konkrétneho dokumentu, čo sa nehodí na legislatívu, ktorá sa mení. Zostáva ako doplnkový, human-readable odkaz ("otvor originál"), nie ako štruktúrovaný compliance-záznam.

Všetky tri constraint-entity (`IfcConstraint`, `IfcObjective`, `IfcMetric`) majú v spec explicitnú poznámku, že nie sú súčasťou štandardizovaného schema subsetu ani implementačnej úrovne — žiadny bežný autorský nástroj (Revit) ich nezapisuje natívne. Pre AIMviewer to nie je prekážka, keďže ide o vlastný enrichment krok + vlastný reader (rovnaký princíp ako pri cross-file spatial enrichmente). Overené: `IfcOpenShell` má napriek tomu vlastný dedikovaný API modul `ifcopenshell.api.constraint` (`add_objective`, `add_metric`, `edit_objective`, `edit_metric`, `assign_constraint`, `unassign_constraint`, `remove_constraint`, `remove_metric`) — entity teda nie sú len teoreticky prítomné v schéme, ale majú aj plnohodnotnú knižničnú podporu na strane zápisu/čítania.

## Odporúčanie pre AIMviewer

- **Primárny zápis požiadavky** (to, čo sa má zobraziť pri kliknutí na prvok): `IfcObjective` cez `IfcRelAssociatesConstraint`, s `ObjectiveQualifier=CODECOMPLIANCE`/`REQUIREMENT` a `ConstraintSource` = názov normy (voľný text).
- **Ak sa v budúcnosti pridá automatizované overovanie**: paralelne viesť aj `IfcClassification`/`IfcClassificationReference` väzbu (rovnaký princíp ako SNIM/CCI), keďže len tá je IDS-checkovateľná.
- **Odkaz na plný text normy pre človeka** (voliteľné, doplnkové): `IfcRelAssociatesDocument` → `IfcDocumentReference`.

## Ako sme na to prišli

Otázka začala všeobecne — "má IFC schema niečo na požiadavky/normy" — s odkazom na IDS. Prvá odpoveď smerovala k `IfcConstraintResource` všeobecne a k `IfcRelAssociatesDocument`/`IfcClassification` ako k dvom alternatívam. Spresnenie zadania ("chcem to vidieť štruktúrovane v AIMvieweri, nie odkaz na konkrétny dokument, lebo vyhláška sa mení, a nechcem riešiť overovanie cez IDS") ukázalo, že pôvodná odpoveď miešala dva odlišné ciele — zápis vs. validácia. To viedlo k rozlíšeniu `IfcObjective` (najpresnejšie sémanticky pre samotný zápis) od `IfcClassification` (jediné IDS-checkovateľné). Následná otázka "podporuje toto IfcOpenShell?" overila, že áno — existuje dedikovaný `ifcopenshell.api.constraint` modul, čo potvrdilo, že cesta cez `IfcObjective` nie je len teoretická schéma-hračka, ale reálne použiteľná v existujúcom tooling stacku projektu.

## Zdroje

**Primárne (schema autorita):**
- `ifc43-docs.standards.buildingsmart.org` — `IfcConstraint`, `IfcObjective`, `IfcObjectiveEnum`, `IfcMetric`, `IfcConstraintResource` (kap. 8.3), `IfcClassification`, `IfcClassificationReference`, `IfcRelAssociatesClassification`, `IfcDocumentReference`, `IfcDocumentInformation`, `IfcRelAssociatesDocument`, `IfcRelAssociates`, `IfcExternalReference` (kap. 8.6)

**Sekundárne / implementačné (nie schema autorita, len kontext):**
- `github.com/buildingSMART/IDS` — Documentation/UserManual (zoznam facetov: Entity, Attribute, Classification, Property, Material, PartOf)
- `technical.buildingsmart.org` — "Referencing bSDD in IDS and IFC" (STEP príklad `IFCCLASSIFICATION`/`IFCCLASSIFICATIONREFERENCE`)
- `docs.ifcopenshell.org` — `ifcopenshell.api.constraint` modul (zoznam funkcií)

## Súvisiace

- [[IfcConstraint]]
- [[IfcObjective]]
- [[IfcMetric]]
- [[IfcClassification]]
- [[IfcDocumentReference]]
- [[Téma - COBie, IFC Psety a nástupca (AOH-BSEM)]]
- [[Glossary]]
