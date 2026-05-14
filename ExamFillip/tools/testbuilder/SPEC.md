# ExamFillip Test Builder — SPECIFICATION

## Overview

ExamFillip Test Builder is a suite of three standalone browser-based tools for creating, organizing, and delivering computer-based tests (CBT). The pipeline flows: **extract raw data → sort into structured folders → generate exam portals**.

All tools are pure HTML/CSS/JS (zero build step, no server, no framework).

---

## Project Structure

```
tools/testbuilder/
├── vectorsort/              # Step 1: Extract questions from PDFs
│   ├── index.html           # Question Sorting Engine V1.3
│   ├── CLAUDE.md
│   ├── README.md
│   └── logo.png
├── testdataextractor/       # Step 2: Compile images + answer key → JSON
│   ├── index.html           # Test Data Extractor V1.1
│   ├── CLAUDE.md
│   └── README.md
├── cbtgenerator/            # Step 3: Generate standalone CBT portal
│   ├── index.html           # CBT Generator V1.1
│   └── CLAUDE.md
└── SPEC.md                  # This file
```

---

## Tool 1: VectorSort — Question Sorting Engine (V1.3)

**File:** `vectorsort/index.html`

**Purpose:** Upload a PDF question paper, visually crop individual questions, tag them with metadata, and save cropped images into an organized folder hierarchy.

### Input
- PDF file (question paper)
- User selects crop regions on each page

### Output
- PNG images saved to local folder via File System Access API
- Answer key `.txt` file generated on demand

### Folder Hierarchy (auto-created)
```
{exam}/{subject}/{difficulty}/{questionType}/{8-char-id}.png
Example: JM/Physics/M/SCQ/AbCd1234.png
```

### Tags
| Tag | Options |
|-----|---------|
| Exam | JM, JA, CET, BITSAT |
| Subject | Physics, Chemistry, Mathematics, English, Logical Reasoning |
| Difficulty | E (Easy), M (Medium), T (Tough) |
| Question Type | SCQ, MCQ, NUM |

### Answer Input
- **SCQ:** Single dropdown (1–4)
- **MCQ:** Checkboxes (multiple correct)
- **NUM:** Free text (integer or decimal)

Answers stored in `localStorage` under key `vectorSortAnswers`. Generated as `answer_key.txt` inside the exam folder.

### Key APIs
- **PDF.js** (v3.4.120) — render PDF pages to canvas
- **File System Access API** (`showDirectoryPicker`, `getDirectoryHandle`, `getFileHandle`, `createWritable`) — save PNG + answer key
- **Canvas 2D** — crop selection, multi-part stacking, preview rendering
- **`crypto.getRandomValues()`** — generate 8-char alphanumeric question IDs

### UI
- Dark theme with animated gradient background
- Green accent glow (`#22c55e`), green crop overlay with drag interaction
- Control bar with logo, upload, folder link, page nav, zoom, tag dropdowns
- Left panel: PDF viewer; Right panel: preview + answer form
- Footer: `© 2026 ExamFillip. All rights reserved. | Question Sorting Engine V1.3`

---

## Tool 2: Test Data Extractor (V1.1)

**File:** `testdataextractor/index.html`

**Purpose:** Takes a folder of already-sorted PNG question images (from VectorSort) plus a manually prepared answer key `.txt` file, and compiles them into a structured `master_db.json` file.

### Input
1. **Workspace folder** — folder tree of `.png` images following the same `{exam}/{subject}/{difficulty}/{questionType}/{id}.png` hierarchy
2. **Answer key file** (`.txt`) — lines in format: `ABCD1234 - 1`

### Output
- `master_db.json` — an object keyed by question ID

### Output JSON Schema
```json
{
  "ABCD1234": {
    "qid": "ABCD1234",
    "image": "JM/Physics/H/SCQ/ABCD1234.png",
    "answer": "1",
    "exam": "JM",
    "subject": "Physics",
    "difficulty": "H",
    "type": "SCQ",
    "marking": {
      "positive": 4,
      "negative": -1
    }
  }
}
```

### Marking Scheme (configurable)
| Type | Positive | Negative (default) |
|------|----------|-------------------|
| SCQ  | 4        | -1 |
| MCQ  | 4        | -2 |
| NUM  | 4        | 0 |

### Parsing logic
- Folder path is parsed at 5 levels of depth: `{exam}/{subject}/{difficulty}/{questionType}/{filename}.png`
- Answer key regex: `/([A-Za-z0-9]{8})\s*-\s*(\d+)/`

### UI
- Glassmorphism card with dark/light theme toggle (saved in `localStorage`)
- File drop zones with visual states (empty / loaded)
- Shimmer gradient button, success pulse animation
- No external dependencies (except Google Fonts: Inter)
- Footer: `© 2026 ExamFillip. All rights reserved. | Test Data Extractor V1.1`

---

## Tool 3: CBT Generator (V1.1)

**File:** `cbtgenerator/index.html`

**Purpose:** Takes the `master_db.json` from the Test Data Extractor and generates a fully functional standalone HTML exam portal that simulates a JEE-style (TCS iON / NTA pattern) computer-based test.

### Input
- `master_db.json` (uploaded via drag/click)

### Output
- Standalone HTML file (self-contained, no server needed)

### Exam Portal Views

| View | Description |
|------|-------------|
| `#admin-panel` | JSON upload, exam name, duration, content analysis |
| `#instruction-view` | Exam instructions with subject breakdown, marking scheme |
| `#portal-view` | Main exam terminal (timer, slabs, question area, palette) |
| `#report-view` | Score summary table (subject-wise) |
| `#analysis-view` | Detailed subject-wise & question-wise analysis with bars |
| `#solutions-view` | Solution browse mode with correct/incorrect markers |

### Question Types Supported
- **SCQ** (Single Choice) — radio-button style, 4 options
- **MCQ** (Multiple Correct) — checkboxes, partial marking (+1 per correct option)
- **NUM** (Numerical) — number input with `step="any"`

### Question Object Schema (accepted by tool)
```json
{
  "qid": "UniqueString",
  "image": "Optional URL to question image",
  "text": "Optional plain text question",
  "subject": "Physics | Chemistry | Maths",
  "type": "SCQ | MCQ | NUM",
  "answer": "1" | "1,2,3" | "25.5",
  "marking": { "positive": 4, "negative": -1 }
}
```

### Evaluation Engine (`evalExam`)
- **SCQ/NUM:** exact string match → positive or negative marks
- **MCQ:** 
  - Any wrong option selected → full negative
  - All correct selected → full positive
  - Subset of correct selected (no wrong ones) → +1 per correct option (partial marking)

### Palette Geometry (JEE-style CSS clip-path)
| Status | Shape | Color |
|--------|-------|-------|
| Not Visited | Default rectangle | White |
| Not Answered | Slanted-top trapezoid | Red (`#ef4444`) |
| Answered | Slanted-bottom trapezoid | Green (`#22c55e`) |
| Marked for Review | Circle | Purple (`#8b5cf6`) |
| Answered & Marked | Circle + green check overlay | Purple + Green |

### Standalone Exporter (`exportStandalone`)
- Injects `portal-view`, `report-view`, `solutions-view`, `submit-modal` HTML
- Embeds `allQ` as JSON + all JS functions
- Outputs a zero-dependency HTML file (only needs CDN for Tailwind + Font Awesome)

### UI
- Tailwind CSS (CDN) + Font Awesome (CDN)
- Navy blue header (`#285c95`), light gray background
- Subject slab navigation, timer countdown
- Right sidebar with grouped question palette and status counts
- Footer: `© 2026 ExamFillip. All rights reserved. | CBT Generator V1.1`

---

## Data Pipeline Flow

```
VectorSort                    Test Data Extractor           CBT Generator
──────────                    ───────────────────           ─────────────
PDF Question Paper ──►        Folder of PNGs   ──►          master_db.json ──►
  Crop questions                + Answer Key.txt               Standalone
  Tag with metadata              → Compile into                 CBT Portal
  Save PNGs + answer_key.txt      master_db.json                HTML file
```

1. **VectorSort** extracts questions from PDFs as cropped PNGs + saves answers
2. **Test Data Extractor** reads the folder structure + answer key → produces `master_db.json`
3. **CBT Generator** ingests `master_db.json` → generates a full JEE-style exam portal HTML

All three tools are independent — each can work in isolation, or feed into the next.

---

## Cross-Cutting Conventions

### Question ID
- 8-character alphanumeric, generated by `crypto.getRandomValues()` in VectorSort
- Must be unique within a test bank

### Folder Convention
```
{exam}/{subject}/{difficulty}/{questionType}/{qid}.png
```
This is the **single source of truth** for VectorSort output and Test Data Extractor input.

### Answer Key Format
```
ABCD1234 - 1
EFGH5678 - 3
```
One line per question. Regex: `/([A-Za-z0-9]{8})\s*-\s*(\d+)/`

### localStorage Keys
| Key | Tool | Purpose |
|-----|------|---------|
| `vectorSortAnswers` | VectorSort | Stored question answers before generating answer key |
| `examResults` | CBT Generator | Cached exam results for analysis/solutions views |
| `theme` | Test Data Extractor | Persisted dark/light preference |

---

## Browser Requirements

| API | Needed By | Supported In |
|-----|-----------|-------------|
| File System Access API | VectorSort (save) | Chrome 90+, Edge 90+, Opera 76+ |
| `webkitdirectory` | Test Data Extractor (folder input) | Chrome, Edge, Safari |
| PDF.js | VectorSort (render) | All modern browsers |
| localStorage | VectorSort, CBT Generator, Test Data Extractor | All modern browsers |

---

## Build & Deploy

- **No build step** — all files are directly openable in a browser
- No npm, no bundler, no server required
- VectorSort requires a web server for PDF.js worker (CORS) or can use CDN
- CBT Generator standalone export is a single self-contained HTML file
