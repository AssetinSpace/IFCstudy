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

---

## GitHub push workflow

Tento vault sa aktualizuje priamo z konverzácií (Claude, claude.ai projekt), nie ručne.

**Ako to funguje:**

1. Fine-grained GitHub token (scope obmedzený len na toto repo, permission "Contents: Read and write") je uložený ako súbor v Project Knowledge daného claude.ai projektu — nie v tomto repe, nie v sandboxe trvalo.
2. Keď v ktoromkoľvek chate v projekte príde pokyn typu *"pushni X do IFCstudy"*:
   - token sa vyhľadá v Project Knowledge,
   - repo sa naklonuje do dočasného sandboxu,
   - nové/upravené MD súbory sa vytvoria podľa pravidiel vyššie (autorita = ifc43-docs, jazykové pravidlá SK/CZ cez `Glossary.md`, wikilinky medzi entitami a témami),
   - zmeny sa commitnú a pushnú cez **HTTPS s tokenom** (nie SSH — port 22 je v sandboxe blokovaný firewallom, funguje len HTTPS na `github.com`),
   - token sa po pushi odstráni z lokálnej git remote URL (ostáva len v Project Knowledge).
3. Sandbox sa medzi jednotlivými chatmi resetuje — nie je to teda "trvalé pripojenie", ale token sa vždy nájde nanovo. Z pohľadu používateľa to funguje ako jeden pokyn v ktoromkoľvek chate v projekte.

**Bezpečnostná poznámka:** token/kľúč nie je v šifrovanom trezore — Project Knowledge je bežné textové úložisko projektu. Scope je preto zámerne obmedzený len na toto jedno repo.

