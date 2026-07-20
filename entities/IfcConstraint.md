# IfcConstraint

Obmedzenie (SK) / Omezení (CZ) — ⚠️ NEPOTVRDENÉ, pozri [[Glossary]].

## Schema fakty

- Zdroj pravdy: [ifc43-docs.standards.buildingsmart.org — IfcConstraint](https://ifc43-docs.standards.buildingsmart.org/IFC/RELEASE/IFC4x3/HTML/lexical/IfcConstraint.htm), kap. 8.3.3.1 (`IfcConstraintResource`)
- **Abstraktný supertyp** dvoch konkrétnych entít: `ONEOF (IfcMetric, IfcObjective)` — sám sa neinštancuje.
- Atribúty: `Name` (povinný), `Description`, `ConstraintGrade` (`IfcConstraintEnum`: HARD/SOFT/ADVISORY/USERDEFINED), `ConstraintSource` (**voľný `IfcLabel` text**, nie URI/odkaz na dokument), `CreatingActor`, `CreationTime`, `UserDefinedGrade`.
- **`ConstraintSource` je kľúčový pre use case "väzba na normu bez odkazu na konkrétnu verziu"** — je to čistý text (napr. `"Vyhláška č. 406/2023 Z.z."`), nie `IfcDocumentReference`, takže sa nemusí (a ani nedá) viazať na konkrétny súbor, ktorý sa časom mení.
- Priradenie k prvku: `IfcRelAssociatesConstraint` (subtyp `IfcRelAssociates`), atribút `RelatingConstraint : IfcConstraint`.
- Externé referencie (klasifikácia/dokument) sa na constraint viažu cez inverzný atribút `HasExternalReferences : SET OF IfcExternalReferenceRelationship` — **nie** cez zrušený `IfcConstraintClassificationRelationship` (IFC4-CHANGE, entita odstránená).
- **NOTE zo spec**: `IfcConstraint` (a subtypy) **nie je súčasťou štandardizovaného schema subsetu ani implementačnej úrovne** — vendor nástroje (Revit) ho bežne nezapisujú.

## Otvorené otázky / poznámky

- Napriek chýbajúcej vendor podpore má `IfcOpenShell` vlastný dedikovaný API modul `ifcopenshell.api.constraint` (`add_objective`, `add_metric`, `edit_objective`, `edit_metric`, `assign_constraint`, `unassign_constraint`, `remove_constraint`, `remove_metric`) — pre AIMviewer pipeline (vlastný enrichment + vlastný reader) je to použiteľné bez ohľadu na vendor podporu.
- Sémanticky presnejší mechanizmus pre "tento prvok podlieha požiadavke X" než `IfcClassification` — classification kategorizuje/taxonomizuje, `IfcObjective` (subtyp) priamo vyjadruje účel/požiadavku (`ObjectiveQualifier = CODECOMPLIANCE`/`REQUIREMENT`).
- Pre strojovo **overiteľnú** (IDS-checkovateľnú) väzbu na normu je vhodnejšie použiť `IfcClassification` — IDS 1.0 nemá "Constraint" facet, len Entity/Attribute/Classification/Property/Material/PartOf. `IfcConstraint`/`IfcObjective` teda slúži na **zápis** požiadavky, nie na jej automatizovanú validáciu cez IDS.

## Súvisiace

- [[IfcObjective]]
- [[IfcMetric]]
- [[IfcClassification]]
- [[IfcDocumentReference]]
- [[Téma - Väzba prvku na normu, vyhlášku a predpis]]
- [[Glossary]]
