# IfcAirTerminal

Vzduchotechnický koncový prvok (výustka/difuzér) — pozri [[Glossary]] (SK/CZ preklad pre "Air Terminal" ⚠️ zatiaľ nenavrhnutý, entity name sa používa anglicky).

## Schema fakty

- Zdroj pravdy: [ifc43-docs.standards.buildingsmart.org — IfcAirTerminal](https://ifc43-docs.standards.buildingsmart.org/IFC/RELEASE/IFC4x3/HTML/lexical/IfcAirTerminal.htm)
- Podtyp `IfcFlowTerminal` → `IfcDistributionFlowElement`. Podľa XSD (`IFC4X3_DEV_dcfeedc.xsd`) má okrem zdedených atribútov iba jeden vlastný: `PredefinedType : IfcAirTerminalTypeEnum` (`diffuser`, `grille`, `louvre`, `register`, `userdefined`, `notdefined`).
- **Žiadny natívny atribút na prietok vzduchu.** Entity samotná nenesie flow rate — to platí naprieč celou `IfcHvacDomain` (rovnaký vzor napr. u `IfcDamper`, `IfcAirTerminalBox`).

## Kde sa zapisuje prietok (m³/h resp. m³/s)

Cez Property Sets, nie cez entity attribute:

| Pset | Property | Typ hodnoty | Úroveň | Účel |
|---|---|---|---|---|
| `Pset_AirTerminalOccurrence` | `AirFlowRate` | `IfcPropertySingleValue` / `IfcVolumetricFlowRateMeasure` | inštancia (occurrence) | konkrétny navrhnutý prietok pre danú výustku |
| `Pset_AirTerminalTypeCommon` | `AirFlowrateRange` | `IfcPropertyBoundedValue` / `IfcVolumetricFlowRateMeasure` | typ (`IfcAirTerminalType`) | katalógový min/max rozsah produktu, nie projektová hodnota |
| `Pset_AirTerminalPHistory` | `AirFlowRate` | `IfcPropertyReferenceValue` → `IfcTimeSeries` | inštancia, časový rad | monitorované/namerané hodnoty (BMS, Digital Twin) |

Priradenie na inštanciu ide cez `IfcRelDefinesByProperties`.

## Jednotky — prečo to "prepisuje na m³/s"

`IfcVolumetricFlowRateMeasure` je derived measure. Jednotka sa neurčuje na entite, ale globálne cez `IfcProject.UnitsInContext` (`IfcUnitAssignment`). Spec (`IfcDerivedUnitEnum`) explicitne definuje `VOLUMETRICFLOWRATEUNIT` ako derived unit typ — teda m³/h je legitímna, normatívne podporená jednotka, pokiaľ je v `IfcUnitAssignment` správne zložená (`IfcDerivedUnit` s `Elements`: `CUBIC_METRE` exponent 1 × `IfcConversionBasedUnit` "hour" exponent -1).

Revit defaultne zapisuje SI derived unit (m³/s) bez ohľadu na Project Units nastavenie — toto je **implementačné správanie Revit IFC exportera**, nie obmedzenie schémy. Pozri [[Téma - Air Terminal prietok a jednotky (m3_h vs m3_s)]] pre celý postup riešenia (export aj AIMviewer strana).

## Otvorené otázky / poznámky

- Overiť v `Office_centrum_Brno.ifc` (VZT.ifc), či `Pset_AirTerminalOccurrence.AirFlowRate` je vôbec exportovaný z Revitu, alebo treba mapovať cez shared/custom parameter.
- Overiť skutočný obsah `IFCDERIVEDUNIT` s `.VOLUMETRICFLOWRATEUNIT.` v exportovanom súbore — diagnostika naplánovaná cez IfcOpenShell skript.

## Súvisiace

- [[IfcElement]]
- [[Téma - Air Terminal prietok a jednotky (m3_h vs m3_s)]]
- [[Téma - Cross-file spatial containment (ETL, IDS, ICDD)]]
