# Téma: Cross-file spatial containment — [[IfcSpace]], IDS, ETL, ICDD

Nadväzuje na [[Téma - spájanie geometrie IfcSpace a IfcElement]] — tu ide špecificky o prípad, keď element a cieľový `IfcSpace` **nie sú v tom istom súbore** (ASR.ifc vs. VZT.ifc).

## Problém

- V ASR.ifc sú elementy na správnom `IfcBuildingStorey`, ale hierarchicky na rovnakej úrovni ako `IfcSpace` — treba ich dostať do `IfcSpace` (v rámci jedného súboru, jednoduchý reassignment `IfcRelContainedInSpatialStructure.RelatingStructure`).
- Vo VZT.ifc majú MEP elementy len `IfcBuildingStorey` (Revit MEP nemá krížovo-disciplínovú logiku) a cieľový `IfcSpace` je v **inom súbore** (ASR.ifc).

## Prečo cross-file containment nie je "iný IFC vzťah"

- `IfcRelContainedInSpatialStructure.RelatingStructure`/`RelatedElements` sú priame odkazy na inštancie (STEP `#123`-štýl) — fungujú len v rámci **jednej entitnej populácie (súboru)**.
- `IfcRoot.GlobalId` má `UNIQUE UR1` vynútenú len v rámci jedného súboru — spec negarantuje jedinečnosť naprieč súbormi (funguje to len vďaka nízkej pravdepodobnosti kolízie 128-bit GUID).
- IFC schéma **vôbec nerieši federáciu naprieč súbormi** ako koncept — preto neexistuje "cross-file verzia" tohto vzťahu.

## Architektúra riešenia

1. **IDS #1** (VZT/RVT export, dodávateľ) — jediná realisticky vymáhateľná požiadavka: element → `IfcBuildingStorey` (nástrojový limit Revitu).
2. **IDS #2** (databáza po ETL) — čistý cieľ: element → `IfcSpace`, bez ohľadu na nástrojové obmedzenia.
3. **ETL** (na strane vlastného nástroja) — geometrická detekcia (solid-in-solid, `ifcopenshell.geom`/`geom.tree`), výsledok = shadow-relationship v databáze (GUID→GUID), nie prepis zdrojových IFC.
4. **Fáza "teraz"**: shadow-relationship stačí v RAM/session pre zobrazenie vo vieweri (tree hierarchy, 3D highlight, filtrovanie musia čerpať z dvoch zdrojov naraz — natívny `IfcRelContainedInSpatialStructure` + syntetický index).
5. **ICDD (ISO 21597)** — cieľ na horizonte rokov, nie teraz. Rovnaká shadow-relationship (GUID→GUID) sa neskôr priamo serializuje ako RDF triple v linksete — žiadny extra transformačný krok.

## Nástroje — čo IfcOpenShell zvládne natívne

| Časť | Natívne? |
|---|---|
| Multi-file read/write, GUID matching | ✅ `ifcopenshell.open()`, `element.GlobalId` |
| Geometrická detekcia | ✅ `ifcopenshell.geom` + `geom.tree` |
| Containment reassignment | ✅ `ifcopenshell.api.spatial` |
| IDS validácia | ✅ natívny `ifctester` modul |
| DB shadow-relationship vrstva | ❌ vlastná aplikačná logika |
| ICDD balenie (ISO 21597 kontajner) | ❌ nemá natívnu podporu (otvorený feature request, GitHub issue #4183) — treba postaviť manuálne cez `rdflib` |

Celá fáza "teraz" (bez ICDD) sa dá postaviť výhradne na IfcOpenShell (LGPL-2.1, zdarma).

## Súvisiace

- [[IfcSpace]]
- [[IfcZone]]
- [[Téma - spájanie geometrie IfcSpace a IfcElement]]
- [[Glossary]] — *Priestorový kontajner*, *Federovaný model*, *RDF Linkset*, *GUID*, *IDS*
