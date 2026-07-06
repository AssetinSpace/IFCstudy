# Téma: Air Terminal prietok vzduchu a jednotky (m³/h vs m³/s) — [[IfcAirTerminal]]

## Problém

1. Kde v IFC 4.3 schéme zapísať, koľko m³/h má fúkať konkrétna výustka (`IfcAirTerminal`)?
2. Prečo sa hodnota v `.ifc` súbore správa, akoby bola v m³/s, hoci v Revit projekte sú Air Flow jednotky nastavené na m³/h?

## Kde sa prietok zapisuje (zhrnutie zo schémy)

`IfcAirTerminal` samotná entita nemá na prietok žiadny natívny atribút — potvrdené aj v XSD (`IFC4X3_DEV_dcfeedc.xsd`), kde má okrem zdedených atribútov len `PredefinedType`. Flow rate ide vždy cez Property Set:

- **Návrhová hodnota (occurrence)**: `Pset_AirTerminalOccurrence.AirFlowRate` — `IfcPropertySingleValue` / `IfcVolumetricFlowRateMeasure`. Toto je správne miesto pre "koľko má táto konkrétna výustka fúkať".
- **Katalógový rozsah (type)**: `Pset_AirTerminalTypeCommon.AirFlowrateRange` — `IfcPropertyBoundedValue`, min/max daného produktu, nie projektová hodnota pre danú miestnosť.
- **Monitorovaná história (Digital Twin/BMS)**: `Pset_AirTerminalPHistory.AirFlowRate` — `IfcPropertyReferenceValue` → `IfcTimeSeries`, relevantné pre CAFM/FM prevádzkové dáta, nie pre návrhový stav.

Detail v [[IfcAirTerminal]].

## Prečo sa to "prepisuje" na m³/s — normatívne zdôvodnenie

`IfcVolumetricFlowRateMeasure` je derived measure — jednotka sa nedefinuje na entite ani na property, ale globálne v `IfcProject.UnitsInContext` (`IfcUnitAssignment.Units`). Spec (`IfcDerivedUnitEnum`) obsahuje explicitnú položku `VOLUMETRICFLOWRATEUNIT`, čo znamená, že m³/h **je** normatívne podporená natívna jednotka pre celý súbor — nie je to len otázka zobrazenia vo vieweri. Aby to fungovalo, musí byť v `IfcUnitAssignment` definovaná `IfcDerivedUnit` typu `VOLUMETRICFLOWRATEUNIT` zložená z:

- `IfcSIUnit(.VOLUMEUNIT., $, .CUBIC_METRE.)` — exponent 1,
- `IfcConversionBasedUnit` "hour" (`IfcMeasureWithUnit` = 3600 × `IfcSIUnit(.TIMEUNIT.,$,.SECOND.)`) — exponent -1.

Ak táto `IfcDerivedUnit` v `IfcUnitAssignment` chýba (alebo je zložená len zo SI base jednotiek: m³ × s⁻¹), **každá** hodnota typu `IfcVolumetricFlowRateMeasure` v súbore — vrátane `Pset_AirTerminalOccurrence.AirFlowRate` — sa interpretuje v m³/s bez ohľadu na to, čo bolo nastavené v autorskom nástroji.

## Revit strana — implementačná poznámka (⚠️ vendor, nie spec)

Revit IFC exporter defaultne generuje `IfcUnitAssignment` s SI derived units (m³/s pre flow rate) a v mnohých verziách toto správanie **ignoruje** nastavenie Project Units → Air Flow, pokiaľ nie je v Export Setup (Advanced tab) explicitne zapnutá voľba použiť project display units pre volumetric flow. Toto je vlastnosť konkrétneho exportera, nie obmedzenie IFC schémy — analogické k už zdokumentovaným Revit exporter chybám (SI base defaults, `GrossFloorArea`=`NetFloorArea` bug, deprecated `IfcBuildingStorey.Elevation`).

Diagnostika: v exportovanom `.ifc` vyhľadať `IFCDERIVEDUNIT` s `.VOLUMETRICFLOWRATEUNIT.` — ak Elements obsahuje len `SECOND` bez `IFCCONVERSIONBASEDUNIT` na hodinu, súbor je v m³/s nezávisle od Revit UI nastavenia.

## AIMviewer strana — správna architektúra (rozhodnutie)

Namiesto hardcodenia prepočtu (`× 3600`) v UI vrstve, AIMviewer má pri načítaní projektu:

1. Prečítať skutočnú definíciu `VOLUMETRICFLOWRATEUNIT` z `IfcUnitAssignment` cez `ifcopenshell.util.unit` — nepredpokladať jednotku.
2. Ak je zdrojová jednotka m³/s, prepočet na m³/h robiť **iba na zobrazovacej vrstve**, nikdy neupravovať hodnotu v zdrojovom `.ifc` (konzistentné s princípom single source of truth, žiadna modifikácia zdrojových súborov).
3. Voliteľne (dlhodobejšia, normatívne čistejšia cesta): opraviť `IfcUnitAssignment` post-processingom cez `ifcopenshell.api.unit`, aby `VOLUMETRICFLOWRATEUNIT` bol skutočne m³/h — deterministické a auditovateľné, v súlade s architektúrou AIM/ICDD.

## Ako sme na to prišli

Pôvodná otázka bola len "kde zapísať prietok" — odpoveď (`Pset_AirTerminalOccurrence.AirFlowRate`) sa našla rýchlo cez Annex B alphabetical properties index. Následne sa ukázalo, že samotná hodnota sa vo výstupe javí ako m³/s napriek m³/h nastaveniu v Revite. Namiesto rovno "over Revit nastavenia" sa najprv overilo, či m³/h je vôbec **schémovo legitímna** natívna jednotka (nie len UI trik) — potvrdené cez `IfcDerivedUnitEnum.VOLUMETRICFLOWRATEUNIT`. Až potom sa oddelili dve odlišné vrstvy problému: (1) čo Revit exporter skutočne zapíše do `IfcUnitAssignment` (vendor-specific, treba overiť priamo na `Office_centrum_Brno.ifc`) a (2) ako sa má AIMviewer k jednotke správať nezávisle od toho, čo autorský nástroj urobí — čítať skutočnú jednotku zo súboru, nie predpokladať pevný prepočtový faktor.

## Otvorené / na overenie

- Overiť priamo v `VZT.ifc` (Office Centrum Brno), či `Pset_AirTerminalOccurrence.AirFlowRate` je vôbec exportovaný, a aká `IfcDerivedUnit` je reálne v `IfcUnitAssignment`.
- Naplánovaný IfcOpenShell diagnostický skript: (a) vypísať aktuálnu jednotku pre `VOLUMETRICFLOWRATEUNIT`, (b) prípadná oprava `IfcUnitAssignment` na m³/h.
- SK/CZ preklady pre "Air Flow Rate" / "Volumetric Flow Rate" zatiaľ nie sú v [[Glossary]] potvrdené — pozri návrh nižšie, čaká na odsúhlasenie.

## Súvisiace

- [[IfcAirTerminal]]
- [[Téma - Cross-file spatial containment (ETL, IDS, ICDD)]]
- [[Glossary]]
