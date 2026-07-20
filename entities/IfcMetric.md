# IfcMetric

Metrika (SK) / Metrika (CZ) — ⚠️ NEPOTVRDENÉ, pozri [[Glossary]].

## Schema fakty

- Zdroj pravdy: [ifc43-docs.standards.buildingsmart.org — IfcMetric](https://ifc43-docs.standards.buildingsmart.org/IFC/RELEASE/IFC4x3/HTML/lexical/IfcMetric.htm), kap. 8.3.3.2
- Subtyp [[IfcConstraint]] — zachytáva **kvantitatívnu** stránku obmedzenia (koľko presne), komplementárne k [[IfcObjective]] (kvalitatívne, prečo).
- Vlastné atribúty: `Benchmark : IfcBenchmarkEnum` (napr. `GREATERTHAN`, `LESSTHAN`, `EQUALTO`), `ValueSource : OPTIONAL IfcLabel`, `DataValue : OPTIONAL IfcMetricValueSelect`, `ReferencePath : OPTIONAL IfcReference` — cesta na konkrétny atribút/property, ktorý sa meria (napr. `AttributeIdentifier="AirFlowRate"` → `Pset_AirTerminalOccurrence.AirFlowRate`).
- Typicky sa nepriraďuje k prvku priamo, ale cez `IfcObjective.BenchmarkValues` (jeden objective môže agregovať viacero metrics cez `LogicalAggregator`).
- **NOTE zo spec**: rovnako ako `IfcConstraint`, nie je súčasťou štandardizovaného schema subsetu.

## Praktický zápis (ifcopenshell.api.constraint)

```python
metric = ifcopenshell.api.constraint.add_metric(f, objective=objective)
ifcopenshell.api.constraint.edit_metric(f, metric=metric, attributes={"Benchmark": "GREATERTHAN"})
```

`add_metric_reference(...)` slúži na nastavenie `ReferencePath`.

## Otvorené otázky / poznámky

- Priamo nadväzuje na otvorenú úlohu overiť, či Revit vôbec exportuje `Pset_AirTerminalOccurrence.AirFlowRate` do VZT.ifc — `ReferencePath` má zmysel len ak daný atribút v modeli reálne existuje.

## Súvisiace

- [[IfcConstraint]]
- [[IfcObjective]]
- [[IfcAirTerminal]]
- [[Téma - Väzba prvku na normu, vyhlášku a predpis]]
- [[Glossary]]
