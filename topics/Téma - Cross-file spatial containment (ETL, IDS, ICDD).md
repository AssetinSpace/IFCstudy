# Téma: Cross-file spatial containment — [[IfcSpace]], IDS, ETL, ICDD

Kontext: [[IfcSpace]] rieši, ako sa MEP element (napr. `IfcAirTerminal` z VZT.ifc) priraďuje k priestoru, do ktorého fyzicky patrí. Tu ide o špecifický, ťažší prípad — element a cieľový `IfcSpace` **nie sú v tom istom súbore** (ASR.ifc vs. VZT.ifc, federovaný model Office_centrum_Brno).

## Problém

- V ASR.ifc sú elementy na správnom `IfcBuildingStorey`, ale hierarchicky na rovnakej úrovni ako `IfcSpace` — treba ich dostať do `IfcSpace`. V rámci jedného súboru je to jednoduchý reassignment `IfcRelContainedInSpatialStructure.RelatingStructure` (`ifcopenshell.api.spatial.assign_container`).
- Vo VZT.ifc majú MEP elementy len `IfcBuildingStorey` — Revit MEP export nemá krížovo-disciplínovú logiku, ktorá by vedela, do ktorej architektonickej miestnosti prvok fyzicky patrí. A aj keby vedela, cieľový `IfcSpace` je v **inom súbore** (ASR.ifc), nie v tom, ktorý Revit práve exportuje.

## Prečo cross-file containment nie je "iný IFC vzťah" (a nie je to len detail implementácie)

Toto je jadro problému, nie povrchná prekážka — treba to chápať štrukturálne:

- `IfcRelContainedInSpatialStructure.RelatingStructure`/`RelatedElements` sú v EXPRESS/STEP definované ako **priame odkazy na inštancie** (fyzicky `#123`-štýl referencie v STEP súbore). Tie fungujú len v rámci **jednej entitnej populácie** — teda jedného `.ifc` súboru. Element vo VZT.ifc nemá spôsob, ako "ukázať" na inštanciu v ASR.ifc, pretože STEP formát nič také nedefinuje.
- `IfcRoot.GlobalId` má `UNIQUE UR1` — táto jedinečnosť je normatívne vynútená **len v rámci jedného súboru**. Naprieč súbormi to funguje len preto, že 128-bit GUID algoritmus má štatisticky zanedbateľnú šancu kolízie — nie preto, že by to IFC schéma garantovala pre federované scenáre.
- Vyplýva z toho: IFC schéma **vôbec nepočíta s federáciou naprieč súbormi** ako s konceptom. Nie je to medzera, ktorú niekto zabudol vyplniť — federácia jednoducho nie je súčasťou modelu, na ktorom je STEP/EXPRESS postavený (jeden súbor = jedna uzavretá entitná populácia).

Dôsledok: neexistuje a nemôže existovať "cross-file verzia" `IfcRelContainedInSpatialStructure`. Vzťah medzi VZT elementom a ASR priestorom musí nutne žiť **mimo** oboch IFC súborov.

## Ako sme na to prišli

Pôvodná otázka bola jednoduchšia — ako preradiť element z `IfcBuildingStorey` do `IfcSpace`. Pri riešení vyšlo najavo, že v rámci jedného súboru je to triviálny reassignment (`assign_container`). Problém sa vyostril, keď sme si uvedomili, že VZT a ASR sú **dva samostatné súbory** — a teda otázka "ako priradiť element do priestoru" sa rozdelila na dve odlišné otázky s odlišnými odpoveďami. Overenie `IfcRoot.GlobalId` (UNIQUE len v populácii) a definície `RelatingStructure`/`RelatedElements` (priame inštančné odkazy) potvrdilo, že ide o štrukturálny limit STEP formátu, nie o medzeru, ktorú by riešil iný, doteraz neobjavený IFC vzťah. Z toho vyplynula potreba riešiť to mimo IFC — najprv sme zvažovali rovno ICDD, no rozhodli sme sa fázovať: teraz len runtime shadow-relationship v RAM, ICDD až na horizonte rokov.

## Architektúra riešenia

1. **IDS #1** (VZT/RVT export, dodávateľ) — jediná realisticky vymáhateľná požiadavka: element → `IfcBuildingStorey`. Dôvod: je to nástrojový limit Revitu, nie nedbalosť modelára — vyžadovať viac by bolo nesplniteľné.
2. **IDS #2** (databáza po ETL) — čistý cieľ: element → `IfcSpace`, bez ohľadu na nástrojové obmedzenia. Toto je požiadavka na *výstup* ETL vrstvy, nie na dodávateľov.
3. **ETL** (na strane vlastného nástroja) — geometrická detekcia (solid-in-solid, `ifcopenshell.geom`/`geom.tree`), výsledok = shadow-relationship v databáze (GUID→GUID), **nie** prepis zdrojových IFC. Dôvod needeštruktívnosti: zachováva princíp "IFC ako single source of truth" (needitované zdrojové súbory), ktorý je konzistentný naprieč celým projektom.
4. **Fáza "teraz"**: shadow-relationship stačí v RAM/session pre zobrazenie vo vieveri. Viewer musí stavať hierarchiu/výber/filter z **dvoch zdrojov naraz** — natívny `IfcRelContainedInSpatialStructure` (in-file) + syntetický index (cross-file) — inak by cross-file vzťahy jednoducho neboli vidno.
5. **ICDD (ISO 21597)** — cieľ na horizonte rokov, nie teraz. Dôvod odloženia: rovnaká shadow-relationship (GUID→GUID) sa dá neskôr priamo serializovať ako RDF triple v linksete bez zmeny dátovej štruktúry — čiže fázovanie nič nezahodí, len odkladá balenie.

## Nástroje — čo IfcOpenShell zvládne natívne

| Časť | Natívne? |
|---|---|
| Multi-file read/write, GUID matching | ✅ `ifcopenshell.open()`, `element.GlobalId` |
| Geometrická detekcia | ✅ `ifcopenshell.geom` + `geom.tree` |
| Containment reassignment | ✅ `ifcopenshell.api.spatial` |
| IDS validácia | ✅ natívny `ifctester` modul |
| DB shadow-relationship vrstva | ❌ vlastná aplikačná logika (IfcOpenShell len dodáva dáta) |
| ICDD balenie (ISO 21597 kontajner) | ❌ nemá natívnu podporu (otvorený feature request, GitHub issue #4183) — treba postaviť manuálne cez `rdflib` |

Celá fáza "teraz" (bez ICDD) sa dá postaviť výhradne na IfcOpenShell (LGPL-2.1, zdarma) — žiadny platený nástroj nie je potrebný.

## Súvisiace

- [[IfcSpace]]
- [[IfcZone]]
- [[IfcBuildingStorey]]
- [[Glossary]] — *Priestorový kontajner*, *Federovaný model*, *RDF Linkset*, *GUID*, *IDS*
