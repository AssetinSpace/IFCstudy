# IfcBuildingStorey

Podlažie (SK) / Podlaží (CZ) — pozri [[Glossary]].

## Schema fakty

- Zdroj pravdy: [ifc43-docs.standards.buildingsmart.org — IfcBuildingStorey](https://ifc43-docs.standards.buildingsmart.org/IFC/RELEASE/IFC4x3/HTML/lexical/IfcBuildingStorey.htm), §5.4.3.10
- Subtyp `IfcSpatialStructureElement` — súčasť hierarchie Site→Building→**Storey**→Space cez `IfcRelAggregates`.
- Slúži ako spatial container pre building elementy aj pre `IfcSpace` (ak nie je ešte priradený hlbšie).
- Atribút `Elevation` (`OPTIONAL IfcLengthMeasure`) je **v IFC 4.3 deprecated**: *"Within Pset_BuildingStoreyCommon use ElevationOfSSLRelative or ElevationOfFFLRelative instead."*

### Quantity Sets

`Qto_BuildingStoreyBaseQuantities`:
- `GrossHeight` — top of construction slab to top of construction slab above (floor-to-floor, konštrukčná výška)
- `NetHeight` — top of construction slab to bottom of construction slab above (bez hrúbky stropu nad)
- `GrossPerimeter`, `GrossFloorArea`, `NetFloorArea`, `GrossVolume`, `NetVolume`

`Qto_BodyGeometryValidation`: `GrossSurfaceArea`, `NetSurfaceArea`, `GrossVolume`, `NetVolume`, `SurfaceGenusBeforeFeatures/AfterFeatures`

### Property Sets (relevantné)

- `Pset_BuildingStoreyCommon` — `ElevationOfSSLRelative` (hrubá/štrukturálna podlaha), `ElevationOfFFLRelative` (hotová podlaha), `SprinklerProtection`, `LoadBearingCapacity`, `GrossPlannedArea`, `NetPlannedArea`
- `Pset_AirSideSystemInformation` — VZT dáta priamo na úrovni podlažia (nielen cez `IfcZone`)
- `Pset_Risk`, `Pset_PropertyAgreement` — FM/Asset Management doména

## Otvorené otázky / poznámky

- ⚠️ **Neoverené na konkrétnom Revit exporte**: predpoklad (nie potvrdený zdroj), že Revit zapisuje starší deprecated `Elevation` atribút namiesto `ElevationOfSSLRelative`/`ElevationOfFFLRelative`. Treba overiť priamo na Office_centrum_Brno.ifc cez IfcOpenShell, nie predpokladať.
- Pozri [[Téma - Výškové kóty priestoru a fire compartment]] pre mapovanie `GrossHeight`/`NetHeight` na klasickú SK/CZ terminológiu (konštrukčná/hrubá/svetlá výška).

## Súvisiace

- [[IfcSpace]]
- [[Téma - Výškové kóty priestoru a fire compartment]]
- [[Glossary]]
