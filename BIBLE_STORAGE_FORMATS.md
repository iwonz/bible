# Bible Storage Formats — Full/Partial Object Mappings
This document specifies **exact serialization rules** for the app data model  
`BibleVersion → Book → Chapter → Verse` across formats.  
Your converter must be able to emit **either the entire translation** or **a single Book / Chapter / Verse** with stable structure.

> **Golden rules**
>
> - `name` and `shortName` must be in the **language of the translation**. Take canonical names from `./bible/books/[iso639-3].json`.
> - Keep all indices stable: `orderIndex`, `actualOrderIndex`, `alternateOrderIndices`.
> - Omit optional fields only when `null` (or model-equivalent). Never invent data.
> - File encodings: **UTF‑8** (no BOM). Newlines: `\n`.
> - Paths (DO NOT DEVIATE):
>   - **Translation:** `[output]/[translation].[formatExtension]`
>   - **Book:** `[output]/books/[translation]-[bookOsisId].[formatExtension]`
>   - **Chapter:** `[output]/books/[translation]-[bookOsisId]/[translation]-[bookOsisId]-[zeroPaddedChapterNumber].[formatExtension]`
>   - **Verse:** `[output]/books/[translation]-[bookOsisId]/[translation]-[bookOsisId]-[zeroPaddedChapterNumber]/[translation]-[bookOsisId]-[zeroPaddedChapterNumber]-[zeroPaddedVerseNumber].[formatExtension]`

For brevity, examples use **one book (Gen)**, **one chapter (Gen.1)**, **one verse (Gen.1.1)** and English names; your emitter must substitute names according to the target translation language.

---

## 1) XML
**When:** Interop, validation/XSLT, archiving.  
**Root:** `<BibleVersion>` → `<categories>` + `<books>` …

### Translation → `[output]/NIV.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<BibleVersion language="en" code="NIV" name="New International Version"
  booksCount="1" chaptersCount="1" versesCount="1" description="Sample">
  <categories>
    <Category name="Old Testament">
      <bookOrderIndices>0</bookOrderIndices>
    </Category>
  </categories>
  <books>
    <Book osisId="Gen" name="Genesis" shortName="Gen" orderIndex="0"
      chaptersCount="1" versesCount="1" actualOrderIndex="0">
      <alternateOrderIndices>0</alternateOrderIndices>
      <chapters>
        <Chapter osisId="Gen.1" name="Genesis 1" shortName="Gen 1"
          orderIndex="0" versesCount="1" actualOrderIndex="0">
          <alternateOrderIndices>0</alternateOrderIndices>
          <verses>
            <Verse osisId="Gen.1.1" name="Genesis 1:1" shortName="Gen 1:1"
              orderIndex="0" actualOrderIndex="0">
              <alternateOrderIndices>0</alternateOrderIndices>
              <text>In the beginning God created the heaven and the earth.</text>
            </Verse>
          </verses>
        </Chapter>
      </chapters>
    </Book>
  </books>
</BibleVersion>
```
### Book → `[output]/books/NIV-Gen.xml`
```xml
<Book osisId="Gen" name="Genesis" shortName="Gen" orderIndex="0"
  chaptersCount="1" versesCount="1" actualOrderIndex="0">
  <alternateOrderIndices>0</alternateOrderIndices>
  <chapters> … </chapters>
</Book>
```
### Chapter → `[output]/books/NIV-Gen/NIV-Gen-001.xml`
```xml
<Chapter osisId="Gen.1" name="Genesis 1" shortName="Gen 1"
  orderIndex="0" versesCount="1" actualOrderIndex="0">
  <alternateOrderIndices>0</alternateOrderIndices>
  <verses> … </verses>
</Chapter>
```
### Verse → `[output]/books/NIV-Gen/NIV-Gen-001/NIV-Gen-001-001.xml`
```xml
<Verse osisId="Gen.1.1" name="Genesis 1:1" shortName="Gen 1:1"
  orderIndex="0" actualOrderIndex="0">
  <alternateOrderIndices>0</alternateOrderIndices>
  <text>In the beginning God created the heaven and the earth.</text>
</Verse>
```
**Refs:** W3C XML; OSIS/USX families (see links below).

---

## 2) JSON
**When:** APIs, apps, analytics.  
**Root:** JSON object mirroring dataclasses.

### Translation → `[output]/NIV.json`
```json
{
  "language": "en",
  "code": "NIV",
  "name": "New International Version",
  "booksCount": 1,
  "chaptersCount": 1,
  "versesCount": 1,
  "description": "Sample",
  "categories": [
    {"name": "Old Testament", "bookOrderIndices": [0], "description": null, "subcategories": []}
  ],
  "books": [
    {
      "osisId": "Gen",
      "name": "Genesis",
      "shortName": "Gen",
      "orderIndex": 0,
      "chaptersCount": 1,
      "versesCount": 1,
      "actualOrderIndex": 0,
      "alternateOrderIndices": [0],
      "chapters": [
        {
          "osisId": "Gen.1",
          "name": "Genesis 1",
          "shortName": "Gen 1",
          "orderIndex": 0,
          "versesCount": 1,
          "actualOrderIndex": 0,
          "alternateOrderIndices": [0],
          "verses": [
            {
              "osisId": "Gen.1.1",
              "name": "Genesis 1:1",
              "shortName": "Gen 1:1",
              "text": "In the beginning God created the heaven and the earth.",
              "orderIndex": 0,
              "actualOrderIndex": 0,
              "alternateOrderIndices": [0]
            }
          ]
        }
      ]
    }
  ]
}
```
### Book → `[output]/books/NIV-Gen.json`
```json
{
  "osisId": "Gen",
  "name": "Genesis",
  "shortName": "Gen",
  "orderIndex": 0,
  "chaptersCount": 1,
  "versesCount": 1,
  "actualOrderIndex": 0,
  "alternateOrderIndices": [0],
  "chapters": [
    {
      "osisId": "Gen.1", "name": "Genesis 1", "shortName": "Gen 1",
      "orderIndex": 0, "versesCount": 1, "actualOrderIndex": 0, "alternateOrderIndices": [0],
      "verses": [
        { "osisId": "Gen.1.1", "name": "Genesis 1:1", "shortName": "Gen 1:1",
          "text": "In the beginning God created the heaven and the earth.",
          "orderIndex": 0, "actualOrderIndex": 0, "alternateOrderIndices": [0] }
      ]
    }
  ]
}
```
### Chapter → `[output]/books/NIV-Gen/NIV-Gen-001.json`
```json
{
  "osisId": "Gen.1",
  "name": "Genesis 1",
  "shortName": "Gen 1",
  "orderIndex": 0,
  "versesCount": 1,
  "actualOrderIndex": 0,
  "alternateOrderIndices": [0],
  "verses": [
    { "osisId": "Gen.1.1", "name": "Genesis 1:1", "shortName": "Gen 1:1",
      "text": "In the beginning God created the heaven and the earth.",
      "orderIndex": 0, "actualOrderIndex": 0, "alternateOrderIndices": [0] }
  ]
}
```
### Verse → `[output]/books/NIV-Gen/NIV-Gen-001/NIV-Gen-001-001.json`
```json
{
  "osisId": "Gen.1.1",
  "name": "Genesis 1:1",
  "shortName": "Gen 1:1",
  "text": "In the beginning God created the heaven and the earth.",
  "orderIndex": 0,
  "actualOrderIndex": 0,
  "alternateOrderIndices": [0]
}
```
**Refs:** json.org.

---


## 3) CSV (RFC 4180)
**When:** BI, spreadsheets. One row = one verse.

**Columns (fixed order):**  
`language,code,name,description,booksCount,chaptersCount,versesCount,bookOsisId,bookName,bookShortName,bookOrderIndex,bookActualOrderIndex,bookAlternateOrderIndices,bookChaptersCount,bookVersesCount,chapterOsisId,chapterName,chapterShortName,chapterOrderIndex,chapterActualOrderIndex,chapterAlternateOrderIndices,chapterVersesCount,verseOsisId,verseName,verseShortName,verseOrderIndex,verseActualOrderIndex,verseAlternateOrderIndices,verseText`

### Translation → `[output]/NIV.csv`
```csv
language,code,name,description,booksCount,chaptersCount,versesCount,bookOsisId,bookName,bookShortName,bookOrderIndex,bookActualOrderIndex,bookAlternateOrderIndices,bookChaptersCount,bookVersesCount,chapterOsisId,chapterName,chapterShortName,chapterOrderIndex,chapterActualOrderIndex,chapterAlternateOrderIndices,chapterVersesCount,verseOsisId,verseName,verseShortName,verseOrderIndex,verseActualOrderIndex,verseAlternateOrderIndices,verseText
en,NIV,"New International Version","Sample",1,1,1,Gen,Genesis,Gen,0,0,"[0]",1,1,Gen.1,"Genesis 1","Gen 1",0,0,"[0]",1,Gen.1.1,"Genesis 1:1","Gen 1:1",0,0,"[0]","In the beginning God created the heaven and the earth."
```
### Book / Chapter / Verse
Same header; filter rows to the requested scope.

**Refs:** RFC 4180.

---

## 4) YAML
See JSON structure; use YAML syntax at the same paths for Translation/Book/Chapter/Verse.  
**Refs:** yaml.org.

---

## 5) CES (XCES)
(Examples mirror the XML shown earlier but wrapped in `<cesDoc>` with `<div type="book">`, `<div type="chapter">`, `<seg type="verse">`.)  
**Refs:** TEI-C CES.

---

## 6) USFM
(Full/Book/Chapter/Verse snippets shown above; restrict to scope.)  
**Refs:** USFM 3.x (UBS/Paratext).

---

## 7) USFX
(Emit `<usfx>` root; include only needed `<book>`/`<c>`/`<v>`.)  
**Refs:** eBible USFX; Haiola.

---

## 8) USX
(Emit `<usx>` root; include only needed `<book>`, `<chapter>`, `<para>` + `<verse>`.)  
**Refs:** USX 3.0.

---

## 9) Zefania XML
(Emit `<XMLBIBLE>` root with only selected subtree.)  
**Refs:** Zefania projects.

---

## 10) PAULA
Folder with `primary.txt`, `structure.xml`, and header; scope by cropping segments/tokens to the requested range.  
**Refs:** PAULA / ANNIS.

---

## 11) TEI P5
(Emit minimal TEI header and body; scope by keeping only needed `<div>`/`<ab>`.)  
**Refs:** TEI P5.

---

## 12) Text‑Fabric
(Emit a valid TF package folder; scope by including only nodes/edges needed.)  
**Refs:** Text‑Fabric docs.

---

## 13) BSV
(Pipe‑delimited file with header; filter to scope.)  
**Refs:** CrossWire/BSV usage.

---

## 14) SON (OpenSong)
(Minimal `<song>` doc; scope to subset.)  
**Refs:** OpenSong manual.

---

## 15) YET
(`info` + `data` lines; filter to scope.)  
**Refs:** Quick Bible YET.

---

## 16) VideoPsalm
(Provide USFM/USX/OSIS/OpenSong file at the target path — partials allowed.)  
**Refs:** VideoPsalm import guide.

---

## 17) TXT
(Plain text lines; scope by filtering lines.)

## 18) MD
(Frontmatter + headings; scope by keeping relevant sections.)

## 19) HTML
(Full HTML document; scope by including only the requested entity in the DOM.)

---

## Cross‑format conformance checklist
- **Localization:** `name` / `shortName` from `./bible/books/[lang].json` (translation language).
- **Counts:** `booksCount`, `chaptersCount`, `versesCount` must equal emitted content.
- **IDs:** exact `osisId` everywhere; verse display names follow language rules.
- **Indices:** emit ordering fields wherever schema permits (attributes, features, or comments).
- **Paths:** exactly match those defined at the top.

## Sources (authoritative)
- USFM 3.x — https://ubsicap.github.io/usfm/
- USX 3.0 — https://ubsicap.github.io/usx/
- USFX — https://ebible.org/usfx/ , https://haiola.org/haiola.htm
- OSIS — https://crosswire.org/osis/
- Zefania XML — https://sourceforge.net/projects/zefania-sharp/files/Bibles/
- CES/XCES — https://tei-c.org/activities/projects/corpus-encoding-standard-ces/
- PAULA XML — https://korpling.github.io/paula-xml/unpublished/PAULA%20XML%20Documentation.pdf
- Text‑Fabric — https://annotation.github.io/text-fabric/tf/
- OpenSong — https://www.opensongapp.com/user-manual/songs/create-new-song
- Quick Bible YET — https://docs.google.com/document/d/1SGk70g7R3UfN1MTF5jFE9u5bNCY7J9Jeftiq5RjZA0A/edit
- CSV (RFC 4180) — https://www.rfc-editor.org/rfc/rfc4180
- JSON — https://www.json.org/
- YAML — https://yaml.org/
- HTML5 — https://html.spec.whatwg.org/
