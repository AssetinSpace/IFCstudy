# _Instructions — pravidlá tvorby poznámok v tomto vaulte

> Toto je metasúbor. Neobsahuje IFC obsah — popisuje **ako** sa má obsah v tomto vaulte tvoriť. Slúži ako inštrukcia (napr. pre AI asistenta), podľa ktorej sa pri ďalších pushoch generujú reálne poznámkové súbory (`entities/`, `topics/`, `Glossary.md`).

---

## IFC Reference Authority

Tento vault sa zameriava na porozumenie IFC 4.3 schémy. Jediný autoritatívny zdroj pre akúkoľvek IFC-súvisiacu informáciu je oficiálna buildingSMART dokumentácia:

**Primárny zdroj:** https://ifc43-docs.standards.buildingsmart.org/

### Pravidlá pre IFC otázky

Kedykoľvek sa téma dotýka IFC — entít, atribútov, property setov, vzťahov, dedičnosti, konceptov, geometrických reprezentácií alebo štruktúry schémy — platí:

1. Vždy najprv fetchnúť príslušnú stránku z `ifc43-docs.standards.buildingsmart.org`, než sa odpovie. Nespoliehať sa iba na trénovacie dáta.
2. Používať správne URL vzory:
   - Detail entity: `https://ifc43-docs.standards.buildingsmart.org/IFC/RELEASE/IFC4x3/HTML/lexical/[EntityName].htm`
   - Kapitola 5 (Core schemas): `.../HTML/chapter-5/`
   - Kapitola 6 (Shared schemas): `.../HTML/chapter-6/`
   - Kapitola 7 (Domain schemas): `.../HTML/chapter-7/`
   - Kapitola 8 (Resource schemas): `.../HTML/chapter-8/`
   - Concepts: `.../HTML/concepts/content.html`
   - Annex A (EXPRESS/XSD schema): `.../HTML/annex-a.html`
   - Annex B (Abecedný index): `.../HTML/annex-b.html`
   - Annex C (Strom dedičnosti): `.../HTML/annex-c.html`
3. Citovať presne to, čo hovorí spec — rozlišovať medzi normatívnym textom a vlastnou interpretáciou/komentárom.
4. Nikdy necitovať blogy, fóra, vendor dokumentáciu (Revit, ArchiCAD a pod.) ani third-party tutoriály ako IFC schema autoritu. Tie môžu byť referencované iba ako implementačné príklady, jasne označené ako také.
5. Ak fetchnutá stránka neexistuje alebo vráti chybu, výslovne to uviesť a navrhnúť správnu navigačnú cestu v rámci dokumentácie.

### Kontext autora

Autor je BIM konzultant so zázemím v Sustainable Building Design, so záujmom o Asset Management, Information Management, Asset Intelligence, Digital Twins, Facility Management, stavebníctvo, CDE a CAFM. Poznámky majú podľa možnosti prepájať IFC schema koncepty s týmito doménami. Akademický prístup je vítaný, autor je otvorený novým uhlom pohľadu.

---

## Jazykové pravidlá (SK/CZ)

Vault pokrýva oba trhy — slovenský aj český. Pre všetky IFC termíny platí:

Primárny zdroj prekladov je [[Glossary]]. Vždy keď sa použije IFC termín v slovenčine alebo v češtine, použije sa presne ten preklad, ktorý je v slovníku odsúhlasený.

**Pravidlá:**

- Slovenský termín sa používa pri SK kontexte, český pri CZ kontexte.
- Ak nie je jasné, ktorý trh, uvedú sa oba — formát: *Sada výmer (SK) / Sada výměr (CZ)*.
- Ak sa narazí na termín, ktorý ešte nie je v slovníku, upozorní sa na to a navrhne sa preklad do oboch jazykov — až po odsúhlasení sa používa.
- Nikdy sa nevymýšľa preklad svojvoľne bez upozornenia.
- Anglický IFC termín sa vždy uvádza v pôvodnom tvare (napr. *Quantity Set*, `IfcWall`) a preklad je doplnok, nie náhrada.

---

*Reálne obsahové súbory (entity, témy, slovník) sa tvoria a pushujú podľa týchto pravidiel, nie priamo podľa tohto súboru.*
