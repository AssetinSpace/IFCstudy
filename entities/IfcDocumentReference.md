# IfcDocumentReference / IfcDocumentInformation

Odkaz na dokument / Informácie o dokumente (SK) / Odkaz na dokument / Informace o dokumentu (CZ) — ⚠️ NEPOTVRDENÉ, pozri [[Glossary]].

## Schema fakty

- Zdroj pravdy: [ifc43-docs.standards.buildingsmart.org — IfcDocumentReference](https://ifc43-docs.standards.buildingsmart.org/IFC/RELEASE/IFC4x3/HTML/lexical/IfcDocumentReference.htm) a [IfcDocumentInformation](https://ifc43-docs.standards.buildingsmart.org/IFC/RELEASE/IFC4x3/HTML/lexical/IfcDocumentInformation.htm), kap. 8.6.3.3 a 8.6.3.5
- `IfcDocumentReference` (subtyp `IfcExternalReference`): `Location` (URI konkrétneho dokumentu), `Identification` (napr. `"§12"`), `Name`, `Description`, `ReferencedDocument : OPTIONAL IfcDocumentInformation`. WR1: musí byť vyplnený buď `Name` alebo `ReferencedDocument`.
- `IfcDocumentInformation`: plné metadáta dokumentu — `Identification`, `Name`, `Location` (URI), `Purpose`, `Scope`, `Revision`, `DocumentOwner`, `ValidFrom`/`ValidUntil`, `Status` (`IfcDocumentStatusEnum`).
- Priradenie k prvku: `IfcRelAssociatesDocument.RelatingDocument` (accept. `IfcDocumentSelect` = `IfcDocumentInformation` alebo `IfcDocumentReference`).

## Otvorené otázky / poznámky

- **Zásadné obmedzenie pre requirements/compliance use case**: `Location` je URI na *konkrétny* dokument/verziu. Ak sa norma/vyhláška časom mení (bežný prípad SK/CZ legislatívy), odkaz zastarie alebo sa musí ručne udržiavať — na rozdiel od [[IfcClassification]], kde `Location`/`Edition` sú voliteľné a dá sa pracovať len s `Name`/`Source` bez väzby na verziu.
- IDS 1.0 nemá "Document" facet (len Entity/Attribute/Classification/Property/Material/PartOf) — táto väzba je teda len deskriptívna (pre človeka), nie strojovo overiteľná.
- **Odporúčané použitie v AIMviewer**: `IfcRelAssociatesDocument` ako doplnkový, human-readable odkaz na plný text normy (pre kliknutie "otvor originál"), primárny compliance-nosič je [[IfcClassification]] alebo [[IfcObjective]] — pozri [[Téma - Väzba prvku na normu, vyhlášku a predpis]].

## Súvisiace

- [[IfcClassification]]
- [[IfcConstraint]]
- [[Téma - Väzba prvku na normu, vyhlášku a predpis]]
- [[Glossary]]
