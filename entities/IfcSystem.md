# IfcSystem

Systém (SK) / Systém (CZ) — pozri [[Glossary]].

## Schema fakty

- Zdroj pravdy: [ifc43-docs — IfcSystem](https://ifc43-docs.standards.buildingsmart.org/IFC/RELEASE/IFC4x3/HTML/lexical/IfcSystem.htm)
- `ENTITY IfcSystem SUPERTYPE OF (ONEOF (IfcBuildingSystem, IfcBuiltSystem, IfcDistributionSystem, IfcStructuralAnalysisModel, IfcZone)) SUBTYPE OF (IfcGroup)`.
- Normatívna definícia: organizovaná kombinácia súvisiacich častí zložená pre spoločný účel alebo funkciu — v podstate funkčne súvisiaca agregácia produktov.
- Ako podtyp `IfcGroup` **nemá vlastnú polohu ani shape representation** — je to čisté logické zoskupenie podľa roly, bez ohľadu na polohu prvkov v stavbe.
- Členovia (inštancie `IfcProduct`) sa priraďujú cez `IfcRelAssignsToGroup` (inverzný `IsGroupedBy`). Členovia môžu, ale nemusia byť fyzicky prepojení cez `IfcDistributionPort`.
- INVERSE (4.3): `ServicesBuildings` (`IfcRelServicesBuildings`) a `ServicesFacilities` (`IfcRelReferencedInSpatialStructure`) — dva spôsoby previazania systému na priestorovú štruktúru.

## Súvisiace

- [[IfcDistributionSystem]]
- [[IfcZone]]
- [[Glossary]] — *Systém*
