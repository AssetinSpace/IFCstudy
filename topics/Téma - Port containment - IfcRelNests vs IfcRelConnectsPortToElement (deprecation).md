# Téma: Port containment — [[IfcDistributionPort]] cez `IfcRelNests` vs. deprecated `IfcRelConnectsPortToElement`

Kontext: ako sa v IFC 4.3 presne napája port (napr. hrdlo potrubia) na hostiteľský element (napr. WC misa) — a prečo staršie zdroje/dokumentácia hovoria niečo iné než aktuálny spec text.

## Čo hovorí aktuálny spec (2026, "under development")

Zdroj pravdy: [ifc43-docs — IfcPort](https://ifc43-docs.standards.buildingsmart.org/IFC/RELEASE/IFC4x3/HTML/lexical/IfcPort.htm)

Aktuálna verzia lexikálnej stránky `IfcPort` (build 20260630) hovorí: *"An IfcPort is associated with an IfcElement it belongs to through the objectified relationship IfcRelNests if such port is fixed."* — a ďalej pri `PlacementRelTo`: *"...which is related to the IfcPort by the relationship object IfcRelNests for fixed ports."* Žiadna zmienka o `IfcRelConnectsPortToElement` alebo o "dynamicky pripojenom" porte sa v tomto texte už nenachádza.

Potvrdenie z `IfcRelConnectsPorts` (5.4.3.50): *"Each of the ports is logically contained within the IfcDistributionElement by using the ordered collection IfcRelNests."* + IFC4-CHANGE poznámka: *"Previously the containment of the IfcPort within the IfcDistributionElement had been realized using the IfcRelConnectsPortToElement relationship."*

**Záver:** jediný aktuálne platný mechanizmus port→element je `IfcRelNests`. `IfcRelConnectsPortToElement` zostáva v EXPRESS schéme (`IfcPort.ContainedIn`) len pre spätnú kompatibilitu so staršími súbormi — nemá sa používať pre nové dáta.

## Prečo je to mätúce — dva rôzne stavy tej istej dokumentácie

Staršie/alternatívne zrkadlo tej istej stránky (`standards.buildingsmart.org`, teda nie primárny zdroj tohto projektu, ale historicky rovnaký text) ešte obsahuje pôvodnú vetu: *"...through the objectified relationship IfcRelNests if such port is fixed, or IfcRelConnectsPortToElement if such port is dynamically attached."* Keďže `ifc43-docs.standards.buildingsmart.org` je označený ako dokument "under development" (priebežne sa mení aj v rámci IFC4X3_ADD2), je pravdepodobné, že táto veta bola z lexikálnej stránky vyčistená až neskôr, ako reakcia na definitívne zavedenie deprecation.

Dôsledok pre prácu s dokumentáciou v tomto projekte: **dátum/build číslo dokumentu (napr. "IFC 4.3.2.20260630") je relevantný** — pri sporných miestach sa oplatí всimnúť si build a v prípade rozporu dôverovať vždy najaktuálnejšiemu fetchu z `ifc43-docs`, nie skôr zapamätanému textu.

## Fixný vs. dynamický port — čo z toho zostáva

Rozdiel "fixed"/"dynamically attached" bol pôvodne zavedený, aby sa odlíšili:

- **fixný port** — trvalá súčasť definície elementu/typu (súčasť "výrobku" ako celku),
- **dynamický port** — kontextovo pripojený, nie súčasť pevnej geometrie typu.

Spec toto rozlíšenie nikdy bližšie normatívne nedefinoval (žiadne WHERE pravidlo, žiadny enum) — bolo to len opisné rozlíšenie v prozaickom texte entity. Keďže sa `IfcRelConnectsPortToElement` (mechanizmus pre "dynamický" prípad) deprecoval, **rozdiel sa dnes prakticky stráca**: všetky porty, fixné aj kedysi dynamické, sa nestujú rovnako cez `IfcRelNests`.

## Praktický dopad na AIMviewer / IfcOpenShell

Pri parsovaní `IfcRelNests` pre porty treba počítať s tým, že:

- moderné súbory (Revit 2024+) budú mať port↔element výhradne cez `IfcRelNests`,
- staršie alebo z iných platforiem pochádzajúce súbory môžu ešte obsahovať `IfcRelConnectsPortToElement` — treba naň mať fallback, inak sa časť konektivity potichu stratí.

## Ako sme na to prišli

Pôvodná otázka smerovala len k tomu, ako `IfcDistributionPort` funguje všeobecne a ako sa prejavuje pri Revit exporte. Pri overovaní väzby port↔element (na príklade WC→potrubie) sa objavil odkaz na rozlíšenie "fixed vs. dynamically attached" v jednom zdroji, no pri priamom fetchi aktuálnej `ifc43-docs` stránky táto veta chýbala — zostalo tam len "if such port is fixed", bez druhej vetvy. Toto viedlo k overeniu `IfcRelConnectsPorts`, kde je explicitná IFC4-CHANGE poznámka potvrdzujúca deprecation `IfcRelConnectsPortToElement`. Záver: dokumentácia je živý dokument a rozdiel medzi dvomi mechanizmami sa v priebehu vývoja IFC4/IFC4.3 zomlel na jeden (`IfcRelNests`) — čo korešponduje aj s reálnou zmenou v Revit exportéri (viď [[IfcDistributionPort]], sekcia Otvorené otázky).

## Súvisiace

- [[IfcDistributionPort]]
- [[IfcDistributionSystem]]
- [[Glossary]] — port (⚠️ NEPOTVRDENÉ)
