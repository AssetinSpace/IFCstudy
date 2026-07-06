# IfcElement

Prvok (SK) / Prvek (CZ) — pozri [[Glossary]].

## Schema fakty

- Zdroj pravdy: [ifc43-docs.standards.buildingsmart.org — IfcElement](https://ifc43-docs.standards.buildingsmart.org/IFC/RELEASE/IFC4x3/HTML/lexical/IfcElement.htm)
- Nadtyp pre fyzické objekty modelu (steny, MEP komponenty, atď.) — má vlastnú geometrickú reprezentáciu.
- Priestorová príslušnosť k `IfcSpace`/`IfcBuildingStorey` sa vyjadruje cez `IfcRelContainedInSpatialStructure`.

## Otvorené otázky / poznámky

- Pri prvkoch z iných disciplín ako architektúra (napr. MEP) Revit tento vzťah k `IfcSpace` často nevytvorí — treba dopočítať geometricky.
- Bounding-box test (BABB) je na tento účel nedostatočný — potrebný je test solid-in-solid.

## Súvisiace

- [[IfcSpace]]
- [[Téma - spájanie geometrie IfcSpace a IfcElement]]
