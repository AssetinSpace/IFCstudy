# Téma: Spájanie geometrie — [[IfcSpace]] a [[IfcElement]]

## Problém

Revit IFC exporter nevytvára cross-discipline vzťah priestorovej príslušnosti — napr. `IfcAirTerminal` (MEP) → `IfcSpace` (architektúra) sa v exporte neobjaví, hoci fyzicky prvok v danom priestore leží.

## Riešenie — sémantické obohatenie (semantic enrichment)

- Vzťah sa dopočíta dodatočne geometrickou intersekciou, nie z natívnych IFC vzťahov.
- **Bounding box test (BABB) nestačí** — falošné pozitíva pri prvkoch na hrane priestoru alebo pri zložitejšej geometrii. Potrebný je solid-in-solid test.
- Toto je uznávaný, akademicky podložený postup (nie workaround):
  - Belsky, Sacks, Brilakis (2016) — SeeBIM
  - Bloch & Sacks (2018)
  - 2025 Automation in Construction — GRC paper

## Perzistencia výsledku obohatenia

Tri možnosti, ako naložiť s dopočítaným vzťahom:

1. **On-the-fly v RAM** — počíta sa pri každom otvorení, nič sa nezapisuje.
2. **Zápis späť do IFC** — trvalý zápis `IfcRelContainedInSpatialStructure` do súboru.
3. **ICDD linkset (RDF)** — vzťah sa uloží mimo IFC, v ISO 21597 kontajneri ako samostatný linkset.

Aktuálne zvažovaný prístup: on-the-fly + ICDD počas aktívnej fázy návrhu, zápis späť do IFC až pri odovzdaní/as-built.

## Súvisiace

- [[IfcSpace]]
- [[IfcElement]]
- [[Glossary]] — pojmy *sémantické obohatenie*, *priestorová príslušnosť*
