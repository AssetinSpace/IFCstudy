# IfcZone

Zóna (SK) / Zóna (CZ) — pozri [[Glossary]].

## Schema fakty

- Zdroj pravdy: [ifc43-docs.standards.buildingsmart.org — IfcZone](https://ifc43-docs.standards.buildingsmart.org/IFC/RELEASE/IFC4x3/HTML/lexical/IfcZone.htm), §5.4.3.82
- **Nie je** subtyp `IfcSpatialElement` — je subtyp `IfcSystem` → `IfcGroup`. **Nemá vlastnú geometriu.**
- Nehierarchická: jeden `IfcSpace` môže patriť do 0, 1 alebo N zón súčasne (na rozdiel od `IfcRelAggregates` stromu).
- Mechanizmus priradenia: `IfcRelAssignsToGroup` (nie `IfcRelContainedInSpatialStructure`).
- **WR1 pravidlo**: členmi zóny (`RelatedObjects`) môžu byť len `IfcZone`, `IfcSpace`, `IfcSpatialZone` — **nikdy priamo fyzický element** (napr. `IfcAirTerminal`, `IfcDuctSegment`).
- Odporúčané `ObjectType` hodnoty zo spec: `FireCompartment`, `ElevatorShaft`, `RisingDuct`, `RunningDuct`.
- Relevantné property sety: `Pset_AirSideSystemInformation` (VZT), `Pset_MaintenanceStrategy`, `Pset_Risk`, `Pset_Condition` (FM/CAFM doména).

## Otvorené otázky / poznámky

- **Nemôže riešiť fire compartment geometricky** — je to čisté logické/administratívne zoskupenie existujúcich `IfcSpace`, bez vlastného telesa. Chýbajú medzery medzi priestormi (hrúbky stien, dosiek, plenum) — potvrdené komunitnou diskusiou (Graphisoft/Archicad fórum, implementačný príklad, nie spec autorita).
- Pre skutočný fyzický obal kompartmentu (presahujúci súčet `IfcSpace`) → pozri [[IfcSpatialZone]].
- Revit HVAC Zone → exportuje sa ako `IfcZone`; Revit Room objekty sa dajú tiež zoskupiť do `IfcZone` (implementačný príklad, OSArch wiki).

## Súvisiace

- [[IfcSpace]]
- [[IfcSpatialZone]]
- [[Téma - Výškové kóty priestoru a fire compartment]]
- [[Glossary]]
