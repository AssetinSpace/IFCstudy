# Téma: Výškové kóty priestoru a fire compartment — [[IfcSpace]], [[IfcBuildingStorey]], [[IfcZone]], [[IfcSpatialZone]]

## Problém

Teoreticky: `IfcSpace` reprezentuje miestnosť podlaha→podhľad, ale požiarny úsek (fire compartment) je od stropu po ďalší strop (slab-to-slab) — vrátane hrúbky podlahy a priestoru nad podhľadom (plenum), ktoré do `IfcSpace` nepatria. Kam to zaradiť?

## Mapovanie na SK/CZ terminológiu (návrh, ⚠️ NEPOTVRDENÉ — pozri [[Glossary]])

| IFC Quantity | Definícia zo spec | SK (návrh) | CZ (návrh) |
|---|---|---|---|
| `Qto_BuildingStoreyBaseQuantities.GrossHeight` | top of slab to top of slab above | **Konštrukčná výška podlažia** | **Konstrukční výška podlaží** |
| `Qto_BuildingStoreyBaseQuantities.NetHeight` | top of slab to bottom of slab above | Hrubá výška podlažia | Hrubá výška podlaží |
| `Qto_SpaceBaseQuantities.Height` | rovnaké rozpätie ako NetHeight, na úrovni miestnosti | Hrubá výška miestnosti/priestoru | Hrubá výška místnosti/prostoru |
| `Qto_SpaceBaseQuantities.FinishFloorHeight` | top of slab to top of flooring | Výška skladby podlahy | Výška skladby podlahy |
| `Qto_SpaceBaseQuantities.FinishCeilingHeight` | top of flooring to bottom of suspended ceiling | **Svetlá výška miestnosti** | **Světlá výška místnosti** |
| `Pset_BuildingStoreyCommon.ElevationOfSSLRelative` | úroveň hrubej (Structural Slab Level) podlahy | Úroveň hrubej podlahy (SSL) | Úroveň hrubé podlahy (SSL) |
| `Pset_BuildingStoreyCommon.ElevationOfFFLRelative` | úroveň hotovej (Finish Floor Level) podlahy | Úroveň čistej podlahy (FFL) | Úroveň čisté podlahy (FFL) |

## Kľúčová korekcia — geometria vs. quantity mená (prečo je to dôležité)

Toto je najdôležitejší poznatok z celej témy, preto podrobnejšie: existujú dve **nezávislé** pravidlá zo spec, ktoré sa dajú ľahko zliať do jedného nesprávneho záveru.

- **Pravidlo A** (§5.4.3.64, Figure B): meno `Height` je sémanticky definované ako slab-to-slab. Toto je fixná definícia mena, nie odporúčanie pre geometriu.
- **Pravidlo B** (§5.4.3.21, Quantity Use Definition): *"Base quantities are... provided by measurement of the correct geometric shape representation of the element."* — teda quantity hodnoty majú byť odmerané zo skutočnej geometrie elementu.

Nesprávny (aj keď lákavý) záver z A+B je: "teda geometria `IfcSpace` musí byť slab-to-slab, aby `Height` sedela." **Toto spec nevyžaduje.** Geometrický rozsah samotného telesa `IfcSpace` je explicitne ponechaný na **Model View Definition / implementer agreement** (koncept Product Geometric Representation) — spec sa k nemu vôbec nevyjadruje. Pravidlo B len hovorí, že *nech už je geometria akákoľvek*, quantity sa má odmerať z nej — nehovorí, akú geometriu si má projekt zvoliť.

Praktický dôsledok: ak je teleso `IfcSpace` modelované netto (floor-to-ceiling) a napriek tomu sa doň zapíše `Height` = slab-to-slab hodnota, táto hodnota **nie je odmeraná z vlastnej geometrie elementu** — je to buď vedomé rozhodnutie (zdokumentovať cez `MethodOfMeasurement`), alebo nekonzistencia medzi menom a skutočným telesom. Obe voľby (netto aj slab-to-slab geometria) sú rovnako schémovo platné — treba si len vybrať a byť interne konzistentný.

## Ako sme na to prišli

Pôvodne padol návrh (Claude), že geometria `IfcSpace` by "mala" byť slab-to-slab, aby `Height` (definovaná ako slab-to-slab) bola priamo odmerateľná — s odvolaním sa na jeden komunitný príspevok (OSArch fórum), že "room goes up to underside of structural floor above". Autor si to spochybnil ("kde máš zdroj, že by to malo byť slab-to-slab") a pri overovaní sa ukázalo, že **spec túto požiadavku vôbec nemá** — šlo o vlastnú dedukciu prezentovanú nesprávne ako citácia. Korekcia: pravidlo o mene (`Height` = slab-to-slab) a pravidlo o meraní (odmerané z geometrie) sú nezávislé; ani jedno z nich nevynucuje konkrétny geometrický rozsah telesa. Toto je príklad, prečo sa v tomto vaulte dôsledne rozlišuje normatívny text od interpretácie — aj keď je interpretácia logicky zvádzajúca, nesmie sa prezentovať ako spec.

## Fire compartment — správne zaradenie

- `IfcZone` (`ObjectType='FireCompartment'`) — **len logické/administratívne zoskupenie** existujúcich `IfcSpace`. Nemá vlastnú geometriu → nerieši medzery (hrúbky stien, plenum). Potvrdené komunitnou diskusiou (Graphisoft/Archicad fórum, implementačný príklad).
- `IfcSpatialZone` (`PredefinedType` fire safety) — **má vlastnú, nezávislú geometriu** — schémovo správna entita pre skutočný fyzický obal kompartmentu presahujúci súčet priestorov (vrátane hrúbky stien a plenumu).
- `IfcRelSpaceBoundary` (2nd level) — prepája `IfcSpace` s fyzickými hraničiacimi elementmi (doska nad/pod) bez zlúčenia geometrie; jeden slab môže byť takto prepojený s viacerými priestormi nad aj pod ním.

Dôvod, prečo sa `IfcZone` sama osebe nedá použiť na fyzický obal: WR1 pravidlo (`IfcZone`) povoľuje ako členov len `IfcSpace`, `IfcZone`, `IfcSpatialZone` — a keďže `IfcZone` nemá vlastnú geometriu, geometria kompartmentu by musela byť odvodená ako súčet geometrií jej členov, čo nezahŕňa hrúbky stien/dosiek medzi nimi. `IfcSpatialZone` tento problém rieši práve tým, že môže mať **vlastné, nezávislé** teleso, nezávislé od súčtu podriadených priestorov.

## Otvorené / na overenie

- Overiť na Office_centrum_Brno.ifc, či Revit zapisuje `IfcBuildingStorey.Elevation` (deprecated) namiesto `Pset_BuildingStoreyCommon.ElevationOfSSLRelative`/`ElevationOfFFLRelative` — nemám priamy potvrdený zdroj, len predpoklad podľa známeho vzorca správania Revit exportéra (viackrát pozorovaný v tomto projekte: napr. `GrossFloorArea`=`NetFloorArea` bug).
- buildingSMART má aktívny návrh technickej správy na `IfcSpatialZone` requirements vrátane fire compartmentation — oblasť sa vyvíja, nie je uzavretá ani na úrovni normy.

## Súvisiace

- [[IfcSpace]]
- [[IfcBuildingStorey]]
- [[IfcZone]]
- [[IfcSpatialZone]]
- [[Glossary]]
