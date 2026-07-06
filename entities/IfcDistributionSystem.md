# IfcDistributionSystem

Distribučný systém (SK) / Distribuční systém (CZ) — pozri [[Glossary]].

## Schema fakty

- Zdroj pravdy: [ifc43-docs — IfcDistributionSystem](https://ifc43-docs.standards.buildingsmart.org/IFC/RELEASE/IFC4x3/HTML/lexical/IfcDistributionSystem.htm)
- `ENTITY IfcDistributionSystem SUPERTYPE OF (ONEOF (IfcDistributionCircuit)) SUBTYPE OF (IfcSystem)`. Atribúty: `LongName : OPTIONAL IfcLabel`, `PredefinedType : OPTIONAL IfcDistributionSystemEnum`.
- Normatívna definícia: sieť navrhnutá na príjem, uskladnenie, udržiavanie, distribúciu alebo riadenie toku distribučného média (spec uvádza príklad teplovodného systému: čerpadlo + nádrž + prepojené potrubie k terminálom).
- Ako podtyp [[IfcSystem]] (→ `IfcGroup`) **nemá vlastnú geometriu ani polohu** — je to skupina, nie fyzický objekt.
- **Členovia** (podtypy `IfcDistributionElement`) sa priraďujú cez `IfcRelAssignsToGroup` (inverzný `IsGroupedBy`). Porty daného `PredefinedType` na priradenom prvku sú implicitne súčasťou systému.
- **Hierarchia** čiastkových systémov cez `IfcRelAggregates` (inverzný `IsDecomposedBy`) — spec príklad: elektrický hlavný okruh sa rozkladá na vetvové okruhy.
- **Priestorové previazanie**: spec pre 4.3 uvádza `IfcRelReferencedInSpatialStructure` na jeden či viac podtypov `IfcSpatialElement` (na úrovni `IfcSystem` existuje aj klasický inverz `ServicesBuildings`/`IfcRelServicesBuildings`).
- `PredefinedType` z `IfcDistributionSystemEnum` (napr. `AIRCONDITIONING`, `VENTILATION`, `EXHAUST`, `CHILLEDWATER`, `DOMESTICCOLDWATER`, `ELECTRICAL`, `LIGHTING`, `FIREPROTECTION`, `USERDEFINED`, `NOTDEFINED`). WHERE `CorrectPredefinedType`: pri `USERDEFINED` musí byť vyplnený `ObjectType`.

## IfcDistributionCircuit vs. IfcDistributionSystem

- `IfcDistributionCircuit` je jediný podtyp, normatívne rezervovaný pre spínateľné/odpojiteľné okruhy (v IFC4 nahradil `IfcElectricalCircuit`). Pre HVAC vetvenie sa používa `IfcRelAggregates` nad `IfcDistributionSystem` — nie `IfcDistributionCircuit` ako všeobecná „vetva". Pozri [[Glossary]].

## Otvorené otázky / poznámky

- **Revit (implementačný príklad, nie schéma):** exportér mapuje Revit *System Classification* na hodnotu `IfcDistributionSystemEnum` cez napevno zadaný slovník → `PredefinedType`. Slovník je neúplný; nezmapované klasifikácie padnú na `NOTDEFINED`. Overovať po exporte, prípadne prepísať parametrom `IfcDistributionSystem` na prvku systému.
- Revit *System Name* (inštancia siete) → atribút `Name`. Revit *System Type* nemá natívny IFC náprotivok (→ `LongName` alebo Pset).

## Súvisiace

- [[IfcSystem]]
- [[IfcElement]]
- [[IfcBuildingStorey]]
- [[Téma - Revit MEP systémy do IfcDistributionSystem (klasifikácia, typ, názov, fragmentácia)]]
- [[Glossary]] — *Distribučný systém*, *Distribučný okruh*
