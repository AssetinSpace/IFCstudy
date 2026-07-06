# Téma: Farba a vzhľad v IFC (baking vs. viewer)

Kontext: dá sa vo `.ifc` držať aj to, akou farbou sa má prvok zobrazovať, alebo to musí riešiť až viewer? A ako to súvisí s obarvením MEP podľa systému.

## Odpoveď: IFC má vlastnú prezentačnú vrstvu

Áno — farba sa dá zapísať priamo do súboru, nemusí to riešiť viewer. Normatívny reťazec (IFC 4.3, presentation appearance resource):

`IfcStyledItem` → `IfcSurfaceStyle` → `IfcSurfaceStyleShading` (`SurfaceColour`) → `IfcColourRgb`

- [[IfcSurfaceStyle]] nesie štýl povrchu (spec ho označuje ako „definíciu materiálu" v renderovacích aplikáciách).
- `IfcSurfaceStyleShading.SurfaceColour` je `IfcColourRgb` (Red/Green/Blue, 0–1), + voliteľná `Transparency`.
- `IfcStyledItem` viaže štýl buď priamo na geometriu, alebo na materiál.

## Prečo NEpiecť obarvenie podľa systému

Treba rozlíšiť dve rôzne veci:

1. **Farba objektu (materiálová)** — patrí do súboru, IFC ju natívne unesie. Napr. medené potrubie má byť medené v každom vieweri. Pokojne piecť.
2. **Analytické obarvenie podľa systému** (každý VZT systém inou farbou) — do geometrie **nepiecť**. Dôvod (vlastná úvaha, spec to priamo nezakazuje): farba by sa naviazala na jednu jedinú interpretáciu a model prestane byť neutrálny. Pre AIM/FM chceme, aby ten istý model vedel niekto obarviť raz podľa systému, inokedy podľa požiarnej odolnosti, inokedy podľa stavu údržby. To je úloha viewera/konzumenta, ktorý farbí za behu podľa property (System Name, PredefinedType, klasifikácia, Pset). Solibri, BIMcollab, usBIM aj Bonsai/BlenderBIM to vedia.

Inak povedané: ak by sme chceli obarvenie podľa systému natrvalo do IFC, museli by sme každému systému priradiť vlastný Revit materiál len kvôli farbe — hack, ktorý znečistí sémantiku modelu presne v tom, čo sa inde čistí.

## Čistota IFC4.x (dve poznámky)

- V IFC4.x sa už **nepoužíva** medzičlánok `IfcPresentationStyleAssignment` — štýly ako `IfcSurfaceStyle` sa priraďujú priamo cez `IfcStyleAssignmentSelect`. Staršie exportéry to niekedy píšu postaru → pri patchi vyčistiť.
- Tessellovaná geometria (častý MEP export): farba plôch cez `IfcColourRgbList` + `IfcIndexedColourMap`, nie cez jeden `IfcSurfaceStyleShading`. Dobré vedieť pri kontrole, čo exportér reálne zapísal.

## Ako sme na to prišli

Otázka bola binárna — „drží sa farba v IFC, alebo je to vec viewera?". Prvý poznatok: IFC má plnohodnotnú prezentačnú vrstvu, takže technicky áno. Zlom ale bol v tom, že „farba" nie je jedna vec — materiálová farba objektu a analytické obarvenie podľa systému sú dva rôzne zámery. Pre AIM/FM z toho vyplynulo pravidlo: materiálovú farbu pokojne do súboru, ale systémové/analytické obarvenie nechať na viewer a do IFC dať namiesto toho *dáta* (System Name, PredefinedType), podľa ktorých viewer farbí. Tým zostáva model sémanticky neutrálny a znovupoužiteľný.

## Súvisiace

- [[IfcSurfaceStyle]]
- [[IfcDistributionSystem]] — zdroj property na obarvenie podľa systému
- [[Glossary]] — *Štýl povrchu*, *Farba povrchu*
