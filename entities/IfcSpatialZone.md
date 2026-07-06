# IfcSpatialZone

Priestorová zóna (SK) / Prostorová zóna (CZ) — ⚠️ NEPOTVRDENÉ, pozri [[Glossary]].

## Schema fakty

- Zdroj pravdy: [ifc43-docs.standards.buildingsmart.org — IfcSpatialZone](https://ifc43-docs.standards.buildingsmart.org/IFC/RELEASE/IFC4x3/HTML/lexical/IfcSpatialZone.htm), §5.4.3.70
- **Je** subtyp `IfcSpatialStructureElement` (rovnako ako `IfcSpace`) → **má vlastnú, nezávislú geometriu/placement**.
- Nehierarchická a potenciálne prekrývajúca sa dekompozícia projektu podľa funkčného hľadiska (tepelná zóna, konštrukčná zóna, osvetľovacia zóna, fire compartment...).
- Kľúčový rozdiel oproti `IfcZone`: *"A spatial zone might have its independent placement and shape representation, whereas IfcZone is only a grouping of IfcSpace's."*
- Elementy sa viažu cez `IfcRelReferencedInSpatialStructure` (nehierarchicky), nie cez containment.
- `PredefinedType` (IfcSpatialZoneTypeEnum) zahŕňa okrem iného hodnotu pre fire safety/compartmentation.

## Otvorené otázky / poznámky

- **Toto je schémovo správna entita pre fyzický obal fire compartmentu** presahujúci súčet jednotlivých `IfcSpace` (napr. slab-to-slab vrátane hrúbky stien a plenumu nad podhľadom) — na rozdiel od `IfcZone`, ktorá vlastnú geometriu nemá.
- buildingSMART má (k dátumu rešerše) aktívny/prebiehajúci návrh technickej správy na `IfcSpatialZone` use-cases vrátane fire compartmentation — oblasť sa ešte vyvíja, nie je uzavretá ani na úrovni normy.
- Pozri [[Téma - Výškové kóty priestoru a fire compartment]] pre celý kontext (prečo `IfcSpace` samo osebe nestačí).

## Súvisiace

- [[IfcSpace]]
- [[IfcZone]]
- [[Téma - Výškové kóty priestoru a fire compartment]]
- [[Glossary]]
