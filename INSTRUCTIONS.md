# Restrictions
- During the work, you cannot modify or delete `./README.md`, `./INSTRUCTIONS.md`, `./BIBLE_STORAGE_FORMATS.md` and folders: `./bible/books`, `./bible/sources`.

# What needs to be done?
1. Study `./README.md`, `./bible/sources/sources.json` and `./sources/books/*.json`, `./BIBLE_STORAGE_FORMATS.md`.
2. Возьми файл `./sources/RUS_SYNODAL.xml` и распарси его у себя в памяти как следующую структуру: BibleVersion -> Books -> Chapters -> Verses.

```typescript
export enum Language {
  rus = "rus",
  bel = "bel",
  ukr = "ukr",
  hye = "hye",
  kat = "kat",
  pol = "pol",
  eng = "eng",
  ita = "ita",
  spa = "spa",
  deu = "deu",
  fra = "fra",
  zho = "zho",
  grc = "grc",
  lat = "lat",
  heb = "heb",
  arc = "arc"
}

export interface BibleVersion {
  language: Language; // STRICTLY ONLY ISO 639-1 language code
  code: string; // Code of translation (e.g., RUS_SYNODAL, RST, NIV, CUV, LXX, etc.)
  name: string; // Name of translation (using BibleVersion.language)
  description?: string; // Optional description of the translation
  books: Book[]; // List of books in the translation
  booksCount: number; // Number of books in the translation
  chaptersCount: number; // Number of chapters in the translation
  versesCount: number; // Number of verses in the translation
  categories: Category[]; // Сategories for organizing books (using BibleVersion.language namings and applied in BibleVersion.code official classification and rules)
}

export interface Category {
  name: string; // Category name, e.g. "Old Testament" (using BibleVersion.language namings and applied in BibleVersion.code official classification and rules)
  description?: string; // Optional description of the category (using BibleVersion.language rules)
  subcategories?: Category[]; // Optional nested subcategories, e.g. "Pentateuch" (using BibleVersion.language namings and applied in BibleVersion.code official classification and rules)
  bookOrderIndices: number[]; // Indexes of books within 'books' belonging to this category (using BibleVersion.language rules and applied in BibleVersion.code official classification and rules)
}

export interface Book {
  osisId: string; // OSIS ID of book (e.g., Gen, Exod)
  name: string; // Name of book (in BibleVersion.language)
  shortName: string; // Short name of book (using BibleVersion.language rules)
  orderIndex: number; // Standard order index of book in translation
  actualOrderIndex?: number; // Alternative numeric order (e.g., chronological or thematic)
  alternateOrderIndices?: number[]; // Other alternative numeric orders
  chapters: Chapter[]; // List of chapters in book
  chaptersCount: number; // Number of chapters in book
  versesCount: number; // Number of verses in book
}

export interface Chapter {
  osisId: string; // OSIS ID of chapter (e.g., Gen.1)
  name: string; // Name of chapter (in BibleVersion.language)
  shortName: string; // Short name of chapter (using BibleVersion.language)
  orderIndex: number; // Standard order index of chapter in book
  actualOrderIndex?: number; // Alternative numeric order
  alternateOrderIndices?: number[]; // Other alternative orders
  verses: Verse[]; // List of verses in chapter
  versesCount: number; // Number of verses in chapter
}

export interface Verse {
  osisId: string; // OSIS ID of verse (e.g., Gen.1.1)
  name: string; // Name of verse (in BibleVersion.language)
  shortName: string; // Short name of verse (using BibleVersion.language rules)
  text: string; // Text content of the verse (in BibleVersion.language)
  orderIndex: number; // Standard order index of verse in chapter
  actualOrderIndex?: number; // Alternative numeric order
  alternateOrderIndices?: number[]; // Other alternative orders
}
```
3. Трансформируй этот перевод в JSON формат, сохранив его в файл `./bible/translations/RUS_SYNODAL.json`. Если нужен вспомогательный скрипт, делай его на Python, а после удаляй.

# Sources
- https://ru.wikipedia.org/wiki/Список_христианских_конфессий and https://en.wikipedia.org/wiki/List_of_Christian_denominations
- https://ru.wikipedia.org/wiki/Библейский_канон and https://en.wikipedia.org/wiki/Biblical_canon
- https://ru.wikipedia.org/wiki/Сокращения_названий_книг_Библии and https://en.wikipedia.org/wiki/Bible_citation