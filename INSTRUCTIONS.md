# Общие знания
- Все вспомогательные скрипты пиши только используя Python.
- Каждый файл в `src/sources` это xml с текстом Библии.
- Вся мета-информация о каждом файле хранится в объекте JSON в файле `src/sources.json`, по ключу равному названию файла.
- В файле `src/books.json` описаны названия всех книг Библии на en, ru, uk, zh, la языках, где нужно по всей структуре используй sblId, osisId, name, shortName для соответсвующих языков именно из этого файла.

# Правила форматов
This guide documents each output format used for storing and exchanging biblical texts. For formats capable of representing structured entities, mappings are aligned to these logical entities: `BibleVersion`, `Category`, `Book`, `Chapter`, `Verse`. Examples favor Unicode and normalized, lossless representations.

Notes:
- Use UTF‑8 everywhere.
- Avoid inventing data: verse text and counts must come from sources.
- Prefer USFM/USX/OSIS for exchange; TEI/Text‑Fabric/PAULA for research; JSON/YAML/XML/CSV/MD/TXT for app/runtime outputs.
- Keep identifiers (`osisId`, `sblId`) exact; keep `orderIndex` stable.

## XML
Extensible Markup Language; tree‑structured container. Many Bible schemas are XML (USX, USFX, OSIS, Zefania).  
Use for interop with validators and XSLT pipelines, long‑term archival.  
Entity mapping: Root = `BibleVersion` with attributes; nested `categories`, `books` → `chapters` → `verses`. Preserve `orderIndex`.

Example:
```xml
<BibleVersion languageCode="en" code="NIV" name="New International Version" booksCount="1" chaptersCount="1" versesCount="1">
  <categories><Category name="Old Testament"><bookOrderIndices>0</bookOrderIndices></Category></categories>
  <books>
    <Book osisId="Gen" sblId="Gen" name="Genesis" shortName="Gen" orderIndex="0" chaptersCount="1" versesCount="1">
      <chapters>
        <Chapter osisId="Gen.1" sblId="Gen 1" name="Genesis 1" shortName="Gen 1" orderIndex="0" versesCount="1">
          <verses><Verse osisId="Gen.1.1" sblId="Gen 1:1" name="Genesis 1:1" shortName="Gen 1:1" orderIndex="0">In the beginning God created the heaven and the earth.</Verse></verses>
        </Chapter>
      </chapters>
    </Book>
  </books>
</BibleVersion>
```
Refs: W3C XML

## JSON
Lightweight data serialization. Use in APIs, web apps, analytics.  
Entity mapping: top‑level `BibleVersion`, arrays for `categories`, `books`, `chapters`, `verses`. Counts must be consistent.

Example:
```json
{
  "languageCode": "en",
  "code": "NIV",
  "name": "New International Version",
  "booksCount": 1,
  "chaptersCount": 1,
  "versesCount": 1,
  "categories": [ { "name": "Old Testament", "bookOrderIndices": [0] } ],
  "books": [
    { "osisId": "Gen", "sblId": "Gen", "name": "Genesis", "shortName": "Gen", "orderIndex": 0,
      "chaptersCount": 1, "versesCount": 1,
      "chapters": [
        { "osisId": "Gen.1", "sblId": "Gen 1", "name": "Genesis 1", "shortName": "Gen 1",
          "orderIndex": 0, "versesCount": 1,
          "verses": [
            { "osisId": "Gen.1.1", "sblId": "Gen 1:1", "name": "Genesis 1:1", "shortName": "Gen 1:1",
              "text": "In the beginning God created the heaven and the earth.", "orderIndex": 0 }
          ] } ] } ] }
```
Refs: json.org

## CSV
Comma‑separated values (RFC 4180). For spreadsheet/DB import, statistics.  
Entity mapping: one verse per row. Columns: `languageCode,code,bookOsisId,...,text`.

Example:
```csv
languageCode,code,bookOsisId,bookSblId,bookOrderIndex,chapterOsisId,chapterSblId,chapterOrderIndex,verseOsisId,verseSblId,verseOrderIndex,text
en,NIV,Gen,Gen,0,Gen.1,Gen 1,0,Gen.1.1,Gen 1:1,0,"In the beginning God created the heaven and the earth."
```
Refs: RFC 4180

## YAML
Human‑readable serialization. Use for configs, small corpora.  
Mapping same as JSON.

Example:
```yaml
languageCode: en
code: NIV
name: New International Version
booksCount: 1
chaptersCount: 1
versesCount: 1
categories: [ { name: Old Testament, bookOrderIndices: [0] } ]
books:
  - osisId: Gen
    sblId: Gen
    name: Genesis
    shortName: Gen
    orderIndex: 0
    chaptersCount: 1
    versesCount: 1
    chapters:
      - osisId: Gen.1
        sblId: "Gen 1"
        name: Genesis 1
        shortName: "Gen 1"
        orderIndex: 0
        versesCount: 1
        verses:
          - osisId: Gen.1.1
            sblId: "Gen 1:1"
            name: "Genesis 1:1"
            shortName: "Gen 1:1"
            text: In the beginning God created the heaven and the earth.
            orderIndex: 0
```
Refs: yaml.org

## CES / XCES
Corpus Encoding Standard (SGML/XML), used for linguistic corpora.  
Example:
```xml
<cesDoc><cesHeader/><text><body><div type="book" n="Gen"><div type="chapter" n="1"><seg type="verse" n="1">In the beginning God created the heaven and the earth.</seg></div></div></body></text></cesDoc>
```
Refs: Vassar CES, TEI CES

## USFM (Unified Standard Format Markers)
Marker‑based plaintext widely used in Paratext. Markers with backslash.  
Example:
```
\id GEN
\h Genesis
	oc1 Genesis
\c 1
 1 In the beginning God created the heaven and the earth.
```
Refs: UBS USFM, Paratext USFM

## USFX
XML derivative of USFM (eBible.org).  
Example:
```xml
<usfx version="2.5"><book id="GEN"><c id="1"><v id="1">In the beginning God created the heaven and the earth.</v></c></book></usfx>
```
Refs: eBible USFX, Haiola

## USX
Unified Scripture XML, close to USFM semantics.  
Example:
```xml
<usx version="3.0"><book code="GEN" style="id"/><chapter number="1" style="c"/><para style="p"><verse number="1" style="v"/>In the beginning God created the heaven and the earth.</para></usx>
```
Refs: UBS USX, Paratext USX

## Zefania XML
Simple XML format, used by community projects.  
Example:
```xml
<XMLBIBLE language="en"><BIBLEBOOK bnumber="1" bname="Genesis"><CHAPTER cnumber="1"><VERS vnumber="1">In the beginning God created the heaven and the earth.</VERS></CHAPTER></BIBLEBOOK></XMLBIBLE>
```
Refs: Zefania repos

## PAULA
Potsdam Interchange Format for Linguistic Annotations. Stand‑off XML layers.  
Example sketch:
```xml
<paula><primaryData uri="genesis.txt"/></paula>
<layer type="tokens"><token xml:id="t1" from="0" to="2">In</token></layer>
<layer type="verse"><seg targets="t1" n="Gen.1.1"/></layer>
```
Refs: PAULA docs, CLARIN, ANNIS

## TEI (Text Encoding Initiative)
Broad XML guidelines for scholarly editions.  
Example:
```xml
<TEI xmlns="http://www.tei-c.org/ns/1.0"><teiHeader/><text><body><div type="book" n="Gen"><div type="chapter" n="1"><ab n="1">In the beginning God created the heaven and the earth.</ab></div></div></body></text></TEI>
```
Refs: TEI Guidelines

## TEXT‑FABRIC
Graph‑based corpus format/API; nodes/edges + feature files.  
Example:
```
@edge
@rel=contains
1 2
1 3

@node
@valueType=str
1=book:Gen
2=chapter:1
3=verse:1

@node
@valueType=str
3=text:In the beginning God created the heaven and the earth.
```
Refs: Text‑Fabric docs

## BSV (Bible Study Version)
Not a public standard. Often pipe‑delimited text.  
Example:
```
GEN|1|1|In the beginning God created the heaven and the earth.
```
Refs: CrossWire formats, Berean Bible project

## SON (OpenSong)
OpenSong XML, used for projection.  
Example:
```xml
<song><title>Genesis 1:1</title><lyrics><verse label="Gen 1:1">In the beginning God created the heaven and the earth.</verse></lyrics></song>
```
Refs: OpenSong formats

## YET
Plain‑text Bible format (Quick Bible/Alkitab).  
Example:
```
info
name=NIV
lang=en
data
Gen 1:1 In the beginning God created the heaven and the earth.
```
Refs: Quick Bible docs

## VideoPsalm
Presentation software, imports many sources (USFM/USX/OSIS/OpenSong).  
Refs: VideoPsalm import guides

## TXT
Plaintext, no semantic markers.  
Convention: prefix with canonical ID.  
Example: `GEN 1:1 In the beginning God created the heaven and the earth.`

## MD (Markdown)
Lightweight markup, for git‑based notes.  
Example:
```markdown
# Genesis 1
**1** In the beginning God created the heaven and the earth.
```

## HTML
### Bible Reader HTML Page — Specification

#### Goals
- Generate a single HTML page for **beautiful, centered Bible text display**.  
- Clear typography, verse numbers, smooth scrolling, easy navigation between books/chapters.  

#### Input
- Parameters: `version`, `book`, `chapter`, list of `verses`.  
- Each verse has `number` + `text`.  

#### Layout
- Centered column (max ~70ch).  
- Readable fonts (serif for text, sans-serif for UI).  
- Verse numbers shown as superscripts, clickable anchors.  
- Sticky header with book/chapter + navigation.  
- Switcher for book/chapter.  
- Prev/Next buttons.  

#### Features
- Deep linking: `#Book.Chapter.Verse`.  
- Smooth scroll to verse when hash present.  
- Copy verse link on click.  
- Light/Dark theme toggle.  
- Font size controls.  

#### Accessibility
- Semantic headings (`h1` for chapter).  
- Verse list with `role="listitem"`.  
- Verse numbers focusable with `aria-label`.  
- Keyboard: arrows for prev/next, focus styles visible.  

#### Example Minimal HTML
```html
<h1>Genesis 1</h1>
<p class="verse" id="Gen.1.1"><sup><a href="#Gen.1.1">1</a></sup> In the beginning God created the heaven and the earth.</p>
<p class="verse" id="Gen.1.2"><sup><a href="#Gen.1.2">2</a></sup> And the earth was without form, and void...</p>
```

## Cross‑format checklist
- `languageCode` must be ISO 639‑1 at root.  
- Root must hold `code`, `name`, `description` and counts.  
- Preserve `orderIndex` across Book/Chapter/Verse.  
- Keep `osisId`, `sblId` exact.  
- `Verse.text` must be verbatim.  
- `Category.bookOrderIndices` must reference actual `Book.orderIndex`.

## Selected tools
usfm2osis/u2o; SWORD tools; Haiola; ptx2pdf

## References
- USFM: https://ubsicap.github.io/usfm/  
- Paratext USFM: https://paratext.org/usfm/  
- USX: https://ubsicap.github.io/usx/  
- ubsicap/usx: https://github.com/ubsicap/usx  
- USFX: https://ebible.org/usfx/  
- Haiola: https://haiola.org/haiola.htm  
- OSIS: https://crosswire.org/osis/  
- Zefania: https://github.com/biblenerd/Zefania-XML-Preservation ; https://sourceforge.net/projects/zefania-sharp/files/Bibles/  
- CES: https://www.cs.vassar.edu/CES/ ; https://tei-c.org/activities/projects/corpus-encoding-standard-ces/  
- PAULA: https://korpling.github.io/paula-xml/unpublished/PAULA%20XML%20Documentation.pdf ; https://standards.clarin.eu/sis/views/view-format.xq?id=fPAULA  
- Text‑Fabric: https://annotation.github.io/text-fabric/tf/ ; https://github.com/annotation/text-fabric  
- OpenSong: https://opensong.org/development/file-formats/ ; https://www.opensongapp.com/user-manual/songs/create-new-song  
- VideoPsalm: https://videopsalm.org/how-to/  
- Quick Bible YET: https://docs.google.com/document/d/1SGk70g7R3UfN1MTF5jFE9u5bNCY7J9Jeftiq5RjZA0A/edit  
- RFC 4180 (CSV): https://www.rfc-editor.org/rfc/rfc4180  
- JSON: https://www.json.org/  
- XML: https://www.w3.org/XML/

# Ограничения
- Не трогать файлы в `src/sources/`, они используются только для чтения.
- Не трогать файл `src/sources.json`, он используется только для чтения.
- Не трогать файл `INSTRUCTION.md` и `README.md`

# Что нужно сделать?
1. Взять все xml файлы в `src/sources/`.
2. Понять мета-информацию по каждому из них.
3. В памяти приложения выстроить чёткую струткру BibleVersion -> Books -> Chapters -> Verses.
4. Для каждой BibleVersion создать папку `bible/[code]`, где `[code]` это код версии Библии.
5. Для каждого формата: `XML`, `JSON`, `CSV`, `YAML`, `CES`, `USFM`, `USFX`, `USX`, `ZEFANIA`, `PAULA`, `TEI`, `TEXT_FABRIC`, `BSV`, `SON`, `YET`, `VIDEO_PLASM`, `TXT`, `MD`, `HTML` создать папку `bible/[code]/[format]`, где `[format]` это формат.
6. Для каждой BibleVersion создать каждый формат используя правила этого формата в каждой папке `bible/[code]/[format]` должен лежать некий `[code].[formatExtension]` со всей Библией и для каждой Book должен быть `bible/[code]/[format]/books/[zeroPaddedBookNumber]-[BookOsisId].[formatExtension]`
7. В конце удалить все скрипты которые ты сгенерировал для выполнения задачи.
