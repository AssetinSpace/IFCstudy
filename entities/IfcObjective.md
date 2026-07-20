# IfcObjective

Cieľ obmedzenia (SK) / Cíl omezení (CZ) — ⚠️ NEPOTVRDENÉ, pozri [[Glossary]].

## Schema fakty

- Zdroj pravdy: [ifc43-docs.standards.buildingsmart.org — IfcObjective](https://ifc43-docs.standards.buildingsmart.org/IFC/RELEASE/IFC4x3/HTML/lexical/IfcObjective.htm)
- Subtyp [[IfcConstraint]] — zachytáva **kvalitatívnu** stránku obmedzenia (účel/dôvod), na rozdiel od kvantitatívneho [[IfcMetric]].
- Vlastné atribúty: `ObjectiveQualifier : IfcObjectiveEnum` (povinný), `UserDefinedQualifier : OPTIONAL IfcLabel`, `LogicalAggregator : OPTIONAL IfcLogicalOperatorEnum` (AND/OR pri agregácii viacerých `BenchmarkValues`), `BenchmarkValues : OPTIONAL SET [1:?] OF IfcConstraint` (typicky `IfcMetric` inštancie).
- `IfcObjectiveEnum` hodnoty priamo relevantné pre requirements/compliance use case: `CODECOMPLIANCE`, `CODEWAIVER`, `REQUIREMENT`, `SPECIFICATION`, `DESIGNINTENT`, `EXTERNAL`, `HEALTHANDSAFETY`, `MERGECONFLICT`, `MODELVIEW`, `PARAMETER`, `TRIGGERCONDITION`, `USERDEFINED`, `NOTDEFINED`.
- Zdedené z `IfcConstraint`: `Name`, `Description`, `ConstraintGrade` (HARD/SOFT/ADVISORY), `ConstraintSource` (voľný text — pozri [[IfcConstraint]]).
- Priradenie k prvku: `IfcRelAssociatesConstraint.RelatingConstraint`.

## Praktický zápis (ifcopenshell.api.constraint)

```python
objective = ifcopenshell.api.constraint.add_objective(f)
ifcopenshell.api.constraint.edit_objective(f, objective=objective, attributes={
    "Name": "Požiadavka na vetranie",
    "ConstraintGrade": "HARD",
    "ConstraintSource": "Vyhláška č. 406/2023 Z.z.",
    "ObjectiveQualifier": "CODECOMPLIANCE",
})
ifcopenshell.api.constraint.assign_constraint(f, products=[prvok], constraint=objective)
```

## Otvorené otázky / poznámky

- Toto je najsémantickejšia zhoda pre use case "prvok podlieha požiadavke vyhlášky X, bez väzby na konkrétny súbor/verziu" — `ConstraintSource` je voľný text, `ObjectiveQualifier=CODECOMPLIANCE` presne pomenúva účel.
- Nie je IDS-checkovateľné (IDS nemá facet pre constraint/objective) — slúži na štruktúrovaný zápis pre AIMviewer UI, nie na automatizovanú validáciu. Pre tú by bolo treba `IfcClassification`.

## Súvisiace

- [[IfcConstraint]]
- [[IfcMetric]]
- [[Téma - Väzba prvku na normu, vyhlášku a predpis]]
- [[Glossary]]
