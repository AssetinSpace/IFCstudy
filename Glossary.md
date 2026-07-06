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

## Procesy / Koncepty

| EN | SK | CZ | Poznámka |
|---|---|---|---|
| Semantic enrichment | Sémantické obohatenie | Sémantické obohacení | proces doplnenia vzťahov, ktoré Revit IFC export neprodukuje |
| Space containment | Priestorová príslušnosť | Prostorová příslušnost | vzťah prvok → priestor |

---

*Dopĺňať priebežne. Nový termín → najprv navrhnúť oba varianty a označiť `⚠️ NEPOTVRDENÉ`, až po odsúhlasení odstrániť značku.*
