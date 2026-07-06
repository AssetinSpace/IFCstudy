# Téma: Revit MEP systémy → [[IfcDistributionSystem]] (klasifikácia, typ, názov, fragmentácia)

Kontext: ako dostať z Revit MEP (VZT model, federovaný Office_centrum_Brno) čistý, sémanticky správny [[IfcDistributionSystem]] — a čo robiť, keď Revit jeden logický systém rozseká na desiatky fragmentov.

## Tri Revit pojmy nie sú hierarchia, ale tri osi

Najčastejší omyl je chápať *System Classification*, *System Type* a *System Name* ako tri úrovne tej istej veci. Sú to tri nezávislé atribúty:

- **System Classification** — inžinierska kategória (Supply Air, Return Air, Exhaust Air…). Pochádza z konektora vnútri rodiny, preto je na inštancii často read-only. Mení sa na úrovni konektora/rodiny, nie prepísaním parametra.
- **System Type** — projektová definícia typu žijúca *pod* jednou klasifikáciou. Viac typov môže zdieľať tú istú klasifikáciu (napr. „Supply Air" a „Supply Air – Lab").
- **System Name** — konkrétna inštancia siete (napr. `M-SUP-02-East`).

## Mapovanie na IFC — čo hovorí spec

- `System Classification` → `PredefinedType` (`IfcDistributionSystemEnum`). Sémantické jadro.
- `System Name` → atribút `Name` inštancie [[IfcDistributionSystem]].
- `System Type` → **nemá natívnu IFC entitu** (IFC nepozná „typ systému"); ak treba, ide do `LongName` alebo vlastného Pset. Nezamieňať s `IfcTypeObject` — ten je pre typy *prvkov*, nie systémov.
- Členovia systému (`IfcDistributionElement`) sa priraďujú cez `IfcRelAssignsToGroup`; hierarchia podsystémov cez `IfcRelAggregates` (viď [[IfcDistributionSystem]]).

## Prečo Revit systémy fragmentuje (a prečo to nie je bug)

Revit vytvára **jednu inštanciu systému na každú fyzicky prepojenú sieť**. Neprepojené úseky (alebo terminály nepretrasované až k zariadeniu) sa stanú samostatnými systémami s automaticky inkrementovaným číslom — odtiaľ `Odvod_ZAR_2 1`, `_2 4`, `_2 5`… Diery v číslovaní (1, 4, 5, 6, 7) typicky znamenajú mazanie a znovuvytváranie.

To je štrukturálne dôsledné: Revit systém = jedna prepojená sieť. Otázka nie je „ako to zlúčiť", ale **akú granularitu chce AIM/FM**. Pre FM je relevantný *logický* systém (vec, ktorú niekto udržiava = „odvod zariadenia ZAR_2"), nie každý kus kanála. Cieľ preto zvyčajne: jeden systém na zariadenie a smer.

Kľúčový mechanizmus na konsolidáciu (implementačný fakt Revitu, nie IFC spec): Revit považuje viacero fyzických sietí za **jeden systém, ak majú rovnaký názov** — takže fragmenty netreba nutne fyzicky spájať, stačí ich priradiť pod jeden názov.

## Postup konsolidácie (pred exportom, nie po ňom)

1. **Diagnostika** v System Browser (zoskupiť podľa System Type) — systémy s 1–2 prvkami sú siroty z odpojeného terminálu/zariadenia.
2. **Zjednotenie názvu** — priradiť prvky fragmentu do jedného systému s rovnakým názvom (Edit System / pridať komponenty), alebo premenovať na spoločný názov.
3. **Alebo fyzicky prepojiť** siete, ktoré patria k sebe (spoločný zberný kanál) → Revit ich zlúči automaticky. Pasca: tvarovky s konektormi nastavenými na inú System Classification bránia zlúčeniu — konektory tvarovky prepnúť na „Fitting".
4. **Stop auto-názvom** — zaviesť konvenciu (napr. `VZT-ODV-ZAR02`) a premenovanie/zápis čistého názvu spraviť dávkovo cez pyRevit.
5. Opačný nástroj `Divide System` (rozdelenie zle prepojeného systému) je dostupný len keď v jednom systéme existuje viac fyzických sietí — tu ho nechceme, ale dobré vedieť.

## Dopad na IFC a prečo riešiť v Revite

Každá Revit inštancia systému = jeden [[IfcDistributionSystem]], `System Name` → `Name`. 50 fragmentov = 50 entít s nezmyselnými názvami → nečitateľné v AIM. Preto **konsolidovať v Revite pred exportom** — to je správne miesto na opravu.

Po konsolidácii: klasifikácia → `PredefinedType` (odvod → `EXHAUST` resp. `VENTILATION`; prívod → `AIRCONDITIONING`/`VENTILATION` — overiť po exporte, slovník exportéra je neúplný). Ak jeden logický systém obsahuje viac vetiev a chceme tú štruktúru zachovať, IFC to unesie cez `IfcRelAggregates` (rodič agreguje `IfcDistributionCircuit`). Revit túto hierarchiu natívne nepostaví — buď zlúčiť na jednu úroveň, alebo doplniť skriptom v IFC (sémantické obohatenie, viď [[Glossary]]).

## Ako sme na to prišli

Najprv sme rozobrali mapovanie troch Revit pojmov — kľúčové bolo uvedomiť si, že klasifikácia/typ/názov nie sú hierarchia, ale tri nezávislé osi, a že `System Type` nemá v IFC náprotivok. Overenie zdrojového kódu exportéra ukázalo, že klasifikácia → `PredefinedType` prechádza cez neúplný napevno zadaný slovník (odtiaľ pády na `NOTDEFINED`). Potom sa objavil reálny model, kde Revit rozsekal jeden systém na desiatky fragmentov — najprv to vyzeralo ako chyba, no ukázalo sa, že Revit korektne robí jednu inštanciu na prepojenú sieť. Zlom bol poznatok, že rovnaký názov = jeden systém, čím sa konsolidácia zjednodušila z „fyzicky prepájať všetko" na „zjednotiť názov". Záver: opravovať pred exportom, lebo `System Name` sa 1:1 premietne do `Name`.

## Súvisiace

- [[IfcDistributionSystem]]
- [[IfcSystem]]
- [[IfcElement]]
- [[Glossary]] — *Distribučný systém*, *Sémantické obohatenie*
