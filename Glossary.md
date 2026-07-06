# Glossary — SK/CZ IFC terminológia

Jediný odsúhlasený zdroj prekladov pre projekt. Formát riadku:

`EN termín — SK preklad (SK) / CZ preklad (CZ) — poznámka`

Termíny označené `⚠️ NEPOTVRDENÉ` čakajú na odsúhlasenie — nepoužívať ako finálne.

## Entity / Schema

| EN | SK | CZ | Poznámka |
|---|---|---|---|
| `IfcSpace` | Priestor | Prostor | |
| `IfcElement` | Prvok | Prvek | |
| `IfcDistributionSystem` | Distribučný systém | Distribuční systém | abstraktný, bez shape representation |
| `IfcDistributionCircuit` | Distribučný okruh | Distribuční okruh | rezervované pre spínateľné/odpojiteľné okruhy (napr. elektrické), nie pre HVAC vetvy |
| `IfcBuildingStorey` | Podlažie | Podlaží | |
| `IfcZone` | Zóna | Zóna | čisté logické zoskupenie, bez geometrie |
| `IfcSpatialZone` | Priestorová zóna | Prostorová zóna | ⚠️ NEPOTVRDENÉ — má vlastnú geometriu, na rozdiel od IfcZone |
| `IfcSystem` | Systém | Systém | podtyp IfcGroup — logické zoskupenie, bez geometrie |
| Predefined Type | Preddefinovaný typ | Předdefinovaný typ | ⚠️ NEPOTVRDENÉ — atribút; pri USERDEFINED nutný ObjectType |

## Výškové kóty (⚠️ NEPOTVRDENÉ — celá sekcia)

| EN | SK | CZ | Poznámka |
|---|---|---|---|
| Gross height (storey) | Konštrukčná výška podlažia | Konstrukční výška podlaží | `Qto_BuildingStoreyBaseQuantities.GrossHeight` |
| Net height (storey) / Height (space) | Hrubá výška podlažia / priestoru | Hrubá výška podlaží / prostoru | slab-to-slab bez hrúbky stropu nad |
| Finish ceiling height | Svetlá výška miestnosti | Světlá výška místnosti | zabehnutý normový termín (STN/ČSN), mapovanie na `FinishCeilingHeight` je môj návrh |
| Structural Slab Level (SSL) | Úroveň hrubej podlahy | Úroveň hrubé podlahy | `ElevationOfSSLRelative` |
| Finish Floor Level (FFL) | Úroveň čistej/hotovej podlahy | Úroveň čisté/hotové podlahy | `ElevationOfFFLRelative` |

## Federácia / cross-file (⚠️ NEPOTVRDENÉ — celá sekcia)

| EN | SK | CZ | Poznámka |
|---|---|---|---|
| Spatial Container | Priestorový kontajner | Prostorový kontejner | |
| Spatial Containment | Priestorové obsiahnutie | Prostorové obsažení | |
| Federated model | Federovaný model | Federovaný model | rovnaký tvar |
| RDF Linkset | RDF prepojenie / linkset | RDF propojení / linkset | |
| GUID | GUID | GUID | bez prekladu |
| IDS (Information Delivery Specification) | IDS | IDS | bez prekladu |

## UI / Nástroje

| EN | SK | CZ | Poznámka |
|---|---|---|---|
| Systems (panel) | Systémy | Systémy | ⚠️ NEPOTVRDENÉ — IFClite GROUPS panel |
| Zones (panel) | Zóny | Zóny | ⚠️ NEPOTVRDENÉ — IFClite GROUPS panel |
| System Classification (Revit) | Klasifikácia systému | Klasifikace systému | ⚠️ NEPOTVRDENÉ — Revit termín; → `PredefinedType` |
| System Type (Revit) | Typ systému | Typ systému | ⚠️ NEPOTVRDENÉ — Revit termín; bez natívneho IFC náprotivku |
| System Name (Revit) | Názov systému | Název systému | ⚠️ NEPOTVRDENÉ — Revit termín; → atribút `Name` |

## HVAC / Prietok vzduchu (⚠️ NEPOTVRDENÉ — celá sekcia)

| EN | SK | CZ | Poznámka |
|---|---|---|---|
| Air Flow Rate | Prietok vzduchu | Průtok vzduchu | `Pset_AirTerminalOccurrence.AirFlowRate` |
| Volumetric Flow Rate | Objemový prietok | Objemový průtok | `IfcVolumetricFlowRateMeasure` |
| Air Terminal | Vzduchotechnický koncový prvok / výustka | Vzduchotechnický koncový prvek / vyústka | `IfcAirTerminal` — kratší hovorový tvar "výustka" bežný v praxi, formálny je prvý |

## Konektivita (⚠️ NEPOTVRDENÉ — celá sekcia)

| EN | SK | CZ | Poznámka |
|---|---|---|---|
| Port | Port | Port | ⚠️ NEPOTVRDENÉ — návrh: ponechať bez prekladu (zabehnutý technický anglicizmus), alternatíva "prípojný bod" znie kostrbato; `IfcDistributionPort` |
| Fixed port | Fixný port | Fixní port | ⚠️ NEPOTVRDENÉ — návrh len opisný, spec pojem bližšie normatívne nedefinuje |

## Procesy / Koncepty

| EN | SK | CZ | Poznámka |
|---|---|---|---|
| Semantic enrichment | Sémantické obohatenie | Sémantické obohacení | proces doplnenia vzťahov, ktoré Revit IFC export neprodukuje |
| Space containment | Priestorová príslušnosť | Prostorová příslušnost | vzťah prvok → priestor |

## Prezentácia / vzhľad (⚠️ NEPOTVRDENÉ — celá sekcia)

| EN | SK | CZ | Poznámka |
|---|---|---|---|
| Surface Style | Štýl povrchu | Styl povrchu | `IfcSurfaceStyle` — v renderoch „definícia materiálu" |
| Surface Colour | Farba povrchu | Barva povrchu | `IfcSurfaceStyleShading.SurfaceColour` (`IfcColourRgb`) |
| Baking (colour into geometry) | Zapečenie farby | Zapečení barvy | anti-vzor pre analytické obarvenie; farbu radšej riešiť vo vieveri |

---

*Dopĺňať priebežne. Nový termín → najprv navrhnúť oba varianty a označiť `⚠️ NEPOTVRDENÉ`, až po odsúhlasení odstrániť značku.*
