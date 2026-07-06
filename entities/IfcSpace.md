# IfcSpace

Priestor (SK) / Prostor (CZ) — pozri [[Glossary]].

## Schema fakty

- Zdroj pravdy: [ifc43-docs.standards.buildingsmart.org — IfcSpace](https://ifc43-docs.standards.buildingsmart.org/IFC/RELEASE/IFC4x3/HTML/lexical/IfcSpace.htm)
- Podtyp `IfcSpatialStructureElement` — súčasť priestorovej hierarchie (Project → Site → Building → Storey → Space).
- Má vlastnú geometrickú reprezentáciu (na rozdiel od napr. `IfcDistributionSystem`, ktorý je abstraktný).

## Quantity Sets

`Qto_SpaceBaseQuantities` — štyri výškové hodnoty (Figure 5.4.3.64.B):
- `Height` — top of slab below to bottom of slab above (**slab-to-slab**, rovnaké rozpätie ako `IfcBuildingStorey.NetHeight`, ale na úrovni miestnosti)
- `FinishFloorHeight` — top of slab below to top of flooring (hrúbka skladby podlahy)
- `FinishCeilingHeight` — top of flooring to bottom of suspended ceiling (**svetlá výška miestnosti**)
- `NetVolume`, `GrossFloorArea`, `NetFloorArea`

⚠️ **Dôležité — normatívne overené (§5.4.3.21, Quantity Use Definition)**: *"Base quantities are... provided by measurement of the correct geometric shape representation of the element."* Tieto hodnoty majú byť odmerané zo skutočnej geometrie `IfcSpace`, nie nezávisle zapísané. Ak sa `Height` (slab-to-slab) zapíše, ale teleso `IfcSpace` je vymodelované len netto (podlaha→podhľad), hodnota **nie je odmeraná z vlastnej geometrie** — je to buď vedomé rozhodnutie zdokumentované cez `MethodOfMeasurement`, alebo nekonzistencia.

## Geometrický rozsah — čo spec NEhovorí (korekcia)

Spec **nemandátuje**, či má teleso `IfcSpace` siahať slab-to-slab alebo len floor-to-ceiling. Jediná normatívna zmienka: *"In cases of inconsistency between the geometric representation of the IfcSpace and the combined geometric representations of the surrounding IfcRelSpaceBoundary, the geometric representation of the space should take priority."* — to rieši len konflikt s hranicami, nie samotný rozsah. Presný geometrický rozsah je explicitne ponechaný na **Model View Definition / implementer agreement** (koncept Product Geometric Representation).

## Otvorené otázky / poznámky

- Revit IFC export **neprodukuje** automaticky vzťah medzi MEP prvkami (napr. `IfcAirTerminal`) a `IfcSpace`, do ktorého fakticky patria — cross-discipline containment sa musí dopočítať dodatočne.
- Pozri [[Téma - spájanie geometrie IfcSpace a IfcElement]] pre detail k tomuto problému a zvolenému riešeniu.
- ⚠️ Nepotvrdené (nemám priamy zdroj): Revit Qto hodnoty (`GrossFloorArea`/`NetFloorArea` a analogicky výškové) sú podľa Autodesk vlastného dev blogu historicky nespoľahlivé — v niektorých prípadoch identické Gross/Net hodnoty namiesto odlišných. Overiť priamo na Office_centrum_Brno.ifc, nespoliehať sa na export bez validácie.
- Fire compartment presahujúci viacero `IfcSpace` (vrátane hrúbky stien/plenumu) → rieši sa cez [[IfcSpatialZone]], nie cez `IfcSpace` samotné. Pozri [[Téma - Výškové kóty priestoru a fire compartment]].

## Súvisiace

- [[IfcElement]]
- [[IfcBuildingStorey]]
- [[IfcZone]]
- [[IfcSpatialZone]]
- [[Téma - spájanie geometrie IfcSpace a IfcElement]]
- [[Téma - Výškové kóty priestoru a fire compartment]]
- [[Téma - Cross-file spatial containment (ETL, IDS, ICDD)]]
