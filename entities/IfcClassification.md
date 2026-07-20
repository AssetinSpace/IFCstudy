# IfcClassification / IfcClassificationReference

Klasifikačný systém / Klasifikačná referencia (SK) / Klasifikační systém / Klasifikační reference (CZ) — ⚠️ NEPOTVRDENÉ, pozri [[Glossary]].

## Schema fakty

- Zdroj pravdy: [ifc43-docs.standards.buildingsmart.org — IfcClassification](https://ifc43-docs.standards.buildingsmart.org/IFC/RELEASE/IFC4x3/HTML/lexical/IfcClassification.htm) a [IfcClassificationReference](https://ifc43-docs.standards.buildingsmart.org/IFC/RELEASE/IFC4x3/HTML/lexical/IfcClassificationReference.htm), kap. 8.6.3.1–8.6.3.2
- `IfcClassification` = celý systém/taxonómia (napr. CCI, Uniclass, alebo v tomto projekte SNIM). Atribúty: `Source` (vydavateľ), `Edition`, `EditionDate`, `Name` (povinný), `Description`, `Location` (URI, **voliteľné**), `ReferenceTokens`.
- **`Location`, `Edition`, `EditionDate` sú OPTIONAL** — systém sa dá zapísať aj bez väzby na konkrétnu verziu/URL, čo je vhodné pre normy/vyhlášky, ktoré sa časom menia (na rozdiel od `IfcDocumentReference`, kde `Location` má zmysel len ako odkaz na konkrétny súbor).
- `IfcClassificationReference` = konkrétny kód/kľúč v rámci systému (subtyp `IfcExternalReference`). Atribúty: `Identification` (kľúč, napr. `"§12"`), `Name` (human-readable), `ReferencedSource` (odkaz späť na `IfcClassification`), `Description`, `Sort`.
- Priradenie k prvku: `IfcRelAssociatesClassification.RelatingClassification` (accept. `IfcClassificationSelect` = `IfcClassification` alebo `IfcClassificationReference`).

## Príklad zápisu (STEP, podľa bSDD referenčného vzoru)

```
/* Source, Edition, EditionDate, Name, Description, Location, ReferenceTokens */
#1=IFCCLASSIFICATION('Ministerstvo životného prostredia SR','2023','2023-12-01',
    'Vyhláška č. 406/2023 Z.z.','Požiadavky na vetranie a vzduchotechniku',$,$);
/* Location, Identification, Name, ReferencedSource, Description, Sort */
#2=IFCCLASSIFICATIONREFERENCE($,'§12','Požiadavky na vetranie hygienických priestorov',#1,$,$);
#4=IFCRELASSOCIATESCLASSIFICATION('...',$,$,$,(#prvok),#2);
```

## Otvorené otázky / poznámky

- **Kľúčové zistenie pre AIMviewer/IDS**: IDS 1.0 má presne 6 facetov — Entity, Attribute, **Classification**, Property, Material, PartOf. Facet pre "Constraint"/"Objective" ani "Document" **neexistuje**. Ak má byť väzba prvku na normu strojovo *overiteľná* cez IDS, musí byť modelovaná ako `IfcClassification`, nie ako `IfcConstraint`/`IfcObjective` ani `IfcDocumentReference`.
- Toto je dôvod, prečo pre AIMviewer/SNIM/CCI zostáva `IfcClassification` primárny nosič compliance-väzby (checkovateľné), zatiaľ čo `IfcObjective` slúži len na štruktúrovaný human-readable zápis (nie checkovateľné) — pozri [[Téma - Väzba prvku na normu, vyhlášku a predpis]].

## Súvisiace

- [[IfcConstraint]]
- [[IfcObjective]]
- [[IfcDocumentReference]]
- [[Téma - Väzba prvku na normu, vyhlášku a predpis]]
- [[Glossary]]
