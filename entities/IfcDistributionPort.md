# IfcDistributionPort (IfcPort)

Port (SK/CZ — ⚠️ NEPOTVRDENÉ, pozri [[Glossary]] — termín "port" zatiaľ nie je v slovníku, navrhujem ponechať bez prekladu ako `IfcDistributionPort`/port, keďže ide o technický pojem bez zabehnutého SK/CZ ekvivalentu v praxi).

## Schema fakty

- Zdroj pravdy: [ifc43-docs — IfcDistributionPort](https://ifc43-docs.standards.buildingsmart.org/IFC/RELEASE/IFC4x3/HTML/lexical/IfcDistributionPort.htm), [ifc43-docs — IfcPort](https://ifc43-docs.standards.buildingsmart.org/IFC/RELEASE/IFC4x3/HTML/lexical/IfcPort.htm), [ifc43-docs — IfcRelConnectsPorts](https://ifc43-docs.standards.buildingsmart.org/IFC/RELEASE/IFC4x3/HTML/lexical/IfcRelConnectsPorts.htm)
- `ENTITY IfcDistributionPort SUBTYPE OF (IfcPort)`. Atribúty: `FlowDirection : OPTIONAL IfcFlowDirectionEnum`, `PredefinedType : OPTIONAL IfcDistributionPortTypeEnum`, `SystemType : OPTIONAL IfcDistributionSystemEnum`.
- `IfcPort` je `ABSTRACT SUPERTYPE OF (ONEOF (IfcDistributionPort)) SUBTYPE OF (IfcProduct)`.
- Normatívna definícia: distribučný port je vstup alebo výstup produktu, cez ktorý môže prúdiť určitá látka — pevná, kvapalná, plynná, alebo elektrina pre výkon či komunikáciu. Flow segmenty (potrubia, VZT potrubia, káble) sa používajú na prepojenie portov medzi produktmi.
- **Porty nemajú vlastnú viditeľnú geometriu** — tá je zachytená v shape representation nadradeného elementu. Porty môžu mať placement určujúci pozíciu a orientáciu spojenia. Ako "subordinate part being fully dependent on the master element", port nemá nezávislý vzťah k priestorovej štruktúre (žiadne vlastné `IfcRelContainedInSpatialStructure`).
- **Podmienka spojenia dvoch portov**: dva porty sa môžu spojiť len ak zdieľajú rovnaký `SystemType` a majú **opačný** `FlowDirection` (jedna strana `SOURCE`, druhá `SINK`).

## Väzba port ↔ element ("napojenie potrubia na WC")

- **Aktuálny normatívny stav (IFC 4.3.2, dokument je "under development", teda živý):** port je asociovaný s elementom cez `IfcRelNests` — a to jediným spôsobom. Staršia veta v dokumentácii, ktorá rozlišovala *"IfcRelNests ak je port fixný, alebo IfcRelConnectsPortToElement ak je dynamicky pripojený"*, bola z aktuálnej verzie lexikálnej stránky odstránená.
- `IfcRelConnectsPortToElement` je **deprecated** (potvrdené priamo v `IfcRelConnectsPorts`: *"Each of the ports is logically contained within the IfcDistributionElement by using the ordered collection IfcRelNests. ... IFC4-CHANGE Previously the containment of the IfcPort within the IfcDistributionElement had been realized using the IfcRelConnectsPortToElement relationship."*). Zostáva v EXPRESS schéme (`ENTITY IfcPort`, inverzný atribút `ContainedIn : SET [0:1] OF IfcRelConnectsPortToElement`) len kvôli spätnej kompatibilite so staršími súbormi (IFC2x2–IFC4).
- **Spojenie dvoch portov naprieč elementmi**: `IfcRelConnectsPorts` — presne dva porty, patriace dvom rôznym elementom, sa spájajú touto relačnou entitou. Môže niesť aj geometriu spojenia.

Mechanizmus pre príklad WC → odpadné potrubie:

```
IfcFlowTerminal (WC misa)
   └─(IfcRelNests)→ IfcDistributionPort (SINK/SOURCE port na WC)
                            │
                  IfcRelConnectsPorts
                            │
IfcFlowSegment (potrubie)
   └─(IfcRelNests)→ IfcDistributionPort (opačný FlowDirection)
```

Element teda nikdy nie je spojený s iným elementom priamo — vždy len cez pár portov.

## Fixný vs. dynamický port — historický kontext

- Rozlíšenie "fixed" vs. "dynamically attached" bolo pôvodne v spec texte `IfcPort` (zachované napr. v staršom zrkadle dokumentácie na `standards.buildingsmart.org`), no v aktuálnej verzii `ifc43-docs.standards.buildingsmart.org` (2026) už z lexikálnej stránky **nie je súčasťou platného normatívneho textu**.
- Interpretácia (moja, spec to bližšie nedefinuje): *fixný* port = trvalá súčasť definície elementu/typu (napr. hrdlo namontované na WC ako výrobná/typová súčasť); *dynamický* port = kontextovo priradený, nie pevná súčasť geometrie typu.
- Prakticky dnes: rozdiel sa už nerealizuje dvomi rôznymi väzbami — všetky porty (bez ohľadu na predošlé rozlíšenie) sa nestujú cez `IfcRelNests`.

## Otvorené otázky / poznámky

- **Revit (implementačný príklad, nie schéma):** Autodesk `revit-ifc` exportér v release `IFC_v19.2.0` explicitne zmenil správanie — *"Replace IfcRelConnectsPortToElement with IfcRelNests for IFC4 export as recommended in IFC4 specifications, allowing non-IfcDistributionElement to participates in the connectivity"*. Aktuálny Revit exportér teda negeneruje deprecated `IfcRelConnectsPortToElement`; port↔element väzba ide vždy cez `IfcRelNests`.
- Pri parsovaní cudzích/legacy IFC súborov (nie z aktuálneho Revitu) treba v IfcOpenShell logike počítať aj s fallbackom na `IfcRelConnectsPortToElement`, keďže staršie exportéry/platformy ju mohli produkovať.
- Relevantné pre AIMviewer: `SystemType` a `FlowDirection` na porte sú kandidát na rýchlu validáciu konzistencie spojenia (SOURCE↔SINK, zhodný SystemType) ešte pred drahšou geometrickou solid-in-solid inferenciou.

## Súvisiace

- [[IfcDistributionSystem]]
- [[IfcElement]]
- [[Téma - Port containment - IfcRelNests vs IfcRelConnectsPortToElement (deprecation)]]
- [[Glossary]] — port (⚠️ NEPOTVRDENÉ)
