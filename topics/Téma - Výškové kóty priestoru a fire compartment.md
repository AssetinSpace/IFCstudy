# Téma: Výškové kóty priestoru a fire compartment — [[IfcSpace]], [[IfcBuildingStorey]], [[IfcZone]], [[IfcSpatialZone]]

## Problém

Teoreticky: `IfcSpace` reprezentuje miestnosť podlaha→podhľad, ale požiarny úsek (fire compartment) je od stropu po ďalší strop (slab-to-slab) — vrátane hrúbky podlahy a priestoru nad podhľadom (plenum), ktoré do `IfcSpace` nepatria. Kam to zaradiť?

## Mapovanie na SK/CZ terminológiu (návrh, ⚠️ NEPOTVRDENÉ)

| IFC Quantity | Definícia zo spec | SK (návrh) | CZ (návrh) |
|---|---|---|---|
| `Qto_BuildingStoreyBaseQuantities.GrossHeight` | top of slab to top of slab above | **Konštrukčná výška podlažia** | **Konstrukční výška podlaží** |
| `Qto_BuildingStoreyBaseQuantities.NetHeight` | top of slab to bottom of slab above | Hrubá výška podlažia | Hrubá výška podlaží |
| `Qto_SpaceBaseQuantities.Height` | rovnaké rozpätie ako NetHeight, na úrovni miestnosti | Hrubá výška miestnosti/priestoru | Hrubá výška místnosti/prostoru |
| `Qto_SpaceBaseQuantities.FinishFloorHeight` | top of slab to top of flooring | Výška skladby podlahy | Výška skladby podlahy |
| `Qto_SpaceBaseQuantities.FinishCeilingHeight` | top of flooring to bottom of suspended ceiling | **Svetlá výška miestnosti** | **Světlá výška místnosti** |
| `Pset_BuildingStoreyCommon.ElevationOfSSLRelative` | úroveň hrubej (Structural Slab Level) podlahy | Úroveň hrubej podlahy (SSL) | Úroveň hrubé podlahy (SSL) |
| `Pset_BuildingStoreyCommon.ElevationOfFFLRelative` | úroveň hotovej (Finish Floor Level) podlahy | Úroveň čistej podlahy (FFL) | Úroveň čisté podlahy (FFL) |

## Kľúčová korekcia — geometria vs. quantity mená

Spec **nemandátuje** geometrický rozsah telesa `IfcSpace` (slab-to-slab vs. floor-to-ceiling) — necháva to na MVD/implementer agreement (§5.4.3.64, Product Geometric Representation koncept). Súčasne ale (§5.4.3.21, Quantity Use Definition) quantity hodnoty majú byť *"provided by measurement of the correct geometric shape representation of the element"*.

→ Ak je teleso netto (floor-to-ceiling) a napriek tomu sa zapíše `Height` = slab-to-slab, hodnota nie je odmeraná z vlastnej geometrie — je to buď vedomé rozhodnutie (zdokumentovať cez `MethodOfMeasurement`), alebo nekonzistencia. **Toto nie je vynútené spec-om, je to vec projektovej konvencie.**

## Fire compartment — správne zaradenie

- `IfcZone` (`ObjectType='FireCompartment'`) — **len logické/administratívne zoskupenie** existujúcich `IfcSpace`. Nemá vlastnú geometriu → nerieši medzery (hrúbky stien, plenum).
- `IfcSpatialZone` (`PredefinedType` fire safety) — **má vlastnú, nezávislú geometriu** — schémovo správna entita pre skutočný fyzický obal kompartmentu presahujúci súčet priestorov.
- `IfcRelSpaceBoundary` (2nd level) — prepája `IfcSpace` s fyzickými hraničiacimi elementmi (doska nad/pod) bez zlúčenia geometrie; jeden slab môže byť takto prepojený s viacerými priestormi nad aj pod ním.

## Otvorené / na overenie

- Overiť na Office_centrum_Brno.ifc, či Revit zapisuje `IfcBuildingStorey.Elevation` (deprecated) namiesto `Pset_BuildingStoreyCommon.ElevationOfSSLRelative`/`ElevationOfFFLRelative` — nemám priamy potvrdený zdroj, len predpoklad podľa známeho vzorca správania Revit exportéra.
- buildingSMART má aktívny návrh technickej správy na `IfcSpatialZone` requirements vrátane fire compartmentation — oblasť sa vyvíja.

## Súvisiace

- [[IfcSpace]]
- [[IfcBuildingStorey]]
- [[IfcZone]]
- [[IfcSpatialZone]]
- [[Glossary]]
