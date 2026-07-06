# IFCstudy

Osobný denník štúdia IFC 4.3 štandardu (Obsidian-style poznámky, wikilinks `[[...]]`). IFC 4.3 sa tu skúma ako kompletná ontológia — nielen Revit export formát — naprieč fyzickými objektmi, procesmi, kontrolami, zdrojmi, aktérmi a dokumentmi. Praktický referenčný model použitý pri poznámkach: `Office_centrum_Brno.ifc`.

## Štruktúra repa

- `README.md` — toto — pravidlá projektu, IFC Reference Authority, jazykové pravidlá
- `Glossary.md` — SK/CZ slovník IFC termínov (jediný zdroj pravdy pre preklady)
- `entities/` — jedna MD na entitu (napr. `IfcSpace.md`, `IfcElement.md`) — schema fakty + prepojenia na témy
- `topics/` — prierezové témy naprieč viacerými entitami (napr. `Téma - spájanie geometrie.md`)
- `log/IFC_export_log.md` — running log rozhodnutí k Brno modelu (presúva sa sem z per-conversation artefaktu)

Poznámky sa prepájajú navzájom cez `[[Wikilinks]]` — entita odkazuje na témy, kde vystupuje, a naopak.

---

## IFC Reference Authority

This project focuses on understanding the IFC 4.3 schema. The sole authoritative source for any IFC-related information is the official buildingSMART documentation:

**Primary source:** https://ifc43-docs.standards.buildingsmart.org/

### Rules for IFC queries

Whenever a question touches IFC — entities, attributes, property sets, relationships, inheritance, concepts, geometry representations, or schema structure — the following applies:

1. Always fetch the relevant page from `ifc43-docs.standards.buildingsmart.org` before answering. Do not rely on training data alone.
2. Use the correct URL patterns:
   - Entity detail: `https://ifc43-docs.standards.buildingsmart.org/IFC/RELEASE/IFC4x3/HTML/lexical/[EntityName].htm`
   - Chapter 5 (Core schemas): `.../HTML/chapter-5/`
   - Chapter 6 (Shared schemas): `.../HTML/chapter-6/`
   - Chapter 7 (Domain schemas): `.../HTML/chapter-7/`
   - Chapter 8 (Resource schemas): `.../HTML/chapter-8/`
   - Concepts: `.../HTML/concepts/content.html`
   - Annex A (EXPRESS/XSD schema): `.../HTML/annex-a.html`
   - Annex B (Alphabetical index): `.../HTML/annex-b.html`
   - Annex C (Inheritance tree): `.../HTML/annex-c.html`
3. Cite what the spec actually says — distinguish between normative spec text and interpretation/commentary.
4. Never cite blog posts, forum threads, vendor documentation (Revit, ArchiCAD, etc.), or third-party tutorials as IFC schema authority. These may be referenced only as implementation examples, clearly labelled as such.
5. If the fetched page is not found or returns an error, say so explicitly and suggest the correct navigation path within the docs.

### Context

User is a BIM Consultant with background in Sustainable Building Design, exploring Asset Management, Digital Twins, Facility Management, CDE, and CAFM. Responses should connect IFC schema concepts to these domains where relevant.

---

## Jazykové pravidlá (SK/CZ)

Projekt pokrýva oba trhy — slovenský aj český. Pre všetky IFC termíny platí:

Primárny zdroj prekladov je [[Glossary|slovník v `Glossary.md`]]. Vždy keď sa použije IFC termín v slovenčine alebo češtine, použije sa presne ten preklad, ktorý je odsúhlasený v slovníku.

**Pravidlá:**

- Slovenský termín sa používa pri SK kontexte, český pri CZ kontexte.
- Ak nie je jasné, ktorý trh, uvedú sa oba — formát: *Sada výmer (SK) / Sada výměr (CZ)*.
- Ak sa narazí na termín, ktorý ešte nie je v slovníku, upozorní sa na to a navrhne sa preklad do oboch jazykov — až po odsúhlasení sa používa.
- Nikdy sa nevymýšľa preklad svojvoľne bez upozornenia.
- Anglický IFC termín sa vždy uvádza v pôvodnom tvare (napr. *Quantity Set*, `IfcWall`) a preklad je doplnok, nie náhrada.
