# IfcSurfaceStyle

Štýl povrchu (SK) / Styl povrchu (CZ) — ⚠️ NEPOTVRDENÉ, pozri [[Glossary]].

## Schema fakty

- Zdroj pravdy: [ifc43-docs — IfcSurfaceStyle](https://ifc43-docs.standards.buildingsmart.org/IFC/RELEASE/IFC4x3/HTML/lexical/IfcSurfaceStyle.htm)
- `ENTITY IfcSurfaceStyle SUBTYPE OF (IfcPresentationStyle); Side : IfcSurfaceSide; Styles : SET [1:5] OF IfcSurfaceStyleElementSelect`.
- Priradenie jedného či viacerých štýlových prvkov k povrchu. Spec poznamenáva, že sa v renderovacích aplikáciách často označuje ako „definícia materiálu".
- Farbu nesie `IfcSurfaceStyleShading` (`SurfaceColour : IfcColourRgb`, `Transparency : OPTIONAL IfcNormalisedRatioMeasure`). `IfcColourRgb` = `Red`/`Green`/`Blue` ako `IfcNormalisedRatioMeasure` (0–1).
- Naviazanie na geometriu cez `IfcStyledItem`: buď priamo na geometrický reprezentačný prvok (`Item` vyplnený), alebo na materiál cez `IfcMaterialDefinitionRepresentation` (vtedy `Item` bez hodnoty).

## Čistota IFC4.x

- V IFC4.x sa už **nepoužíva** medzičlánok `IfcPresentationStyleAssignment` — `IfcStyledItem.Styles` má typ `IfcStyleAssignmentSelect`, takže štýly ako `IfcSurfaceStyle` sa priraďujú priamo. Staršie exportéry to niekedy píšu postaru; pri verifikácii/patchi vyčistiť.
- Tessellovaná (trojuholníková) geometria: farba jednotlivých plôch sa nesie cez `IfcColourRgbList` + `IfcIndexedColourMap`, nie cez jeden `IfcSurfaceStyleShading`.

## Otvorené otázky / poznámky

- **Revit (implementačný príklad, nie schéma):** exportér pečie `IfcSurfaceStyle` z grafiky materiálu prvku, resp. z farby kategórie. Ide o „design" farbu objektu, nie o analytické obarvenie podľa systému.

## Súvisiace

- [[Téma - Farba a vzhľad v IFC (baking vs viewer)]]
- [[Glossary]] — *Štýl povrchu*, *Farba povrchu*
