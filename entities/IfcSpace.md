# IfcSpace

Priestor (SK) / Prostor (CZ) — pozri [[Glossary]].

## Schema fakty

- Zdroj pravdy: [ifc43-docs.standards.buildingsmart.org — IfcSpace](https://ifc43-docs.standards.buildingsmart.org/IFC/RELEASE/IFC4x3/HTML/lexical/IfcSpace.htm)
- Podtyp `IfcSpatialStructureElement` — súčasť priestorovej hierarchie (Project → Site → Building → Storey → Space).
- Má vlastnú geometrickú reprezentáciu (na rozdiel od napr. `IfcDistributionSystem`, ktorý je abstraktný).

## Otvorené otázky / poznámky

- Revit IFC export **neprodukuje** automaticky vzťah medzi MEP prvkami (napr. `IfcAirTerminal`) a `IfcSpace`, do ktorého fakticky patria — cross-discipline containment sa musí dopočítať dodatočne.
- Pozri [[Téma - spájanie geometrie IfcSpace a IfcElement]] pre detail k tomuto problému a zvolenému riešeniu.

## Súvisiace

- [[IfcElement]]
- [[Téma - spájanie geometrie IfcSpace a IfcElement]]
