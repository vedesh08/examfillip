# VectorSort - Question Sorter Webapp

## Project Overview

**Purpose:** A PDF-based question extraction and sorting tool for exam preparation. Users can upload PDF question papers, crop specific questions, and save them organized by exam type and difficulty level into a folder structure.

**Tech Stack:** HTML5, CSS3, Vanilla JavaScript, PDF.js library (v3.4.120 via CDN)

---

## 1. Functions Analysis

### Core Features

| Function | Description | Implementation |
|----------|-------------|----------------|
| **PDF Upload** | Load PDF files from local filesystem | FileReader API + PDF.js |
| **PDF Rendering** | Display PDF pages on canvas | pdf.js `getPage()` + `render()` |
| **Page Navigation** | Navigate between PDF pages | prev/next buttons with `curPage` variable |
| **Zoom Control** | Zoom in/out on PDF view | Scale variable (0.6 to 2.0+) with step 0.2 |
| **Crop Selection** | Drag to select rectangular area | Mouse events (mousedown, mousemove, mouseup) |
| **Preview** | Show cropped area in sidebar | Separate canvas with stacking logic |
| **Add Part** | Append additional crop to existing | Stacks multiple crops vertically with 10px gap |
| **Clear/Reset** | Clear the preview canvas | Reset canvas dimensions to 0 |
| **Save Question** | Export cropped image to folder | File System Access API + canvas.toBlob() |
| **Folder Linking** | Select destination folder | `window.showDirectoryPicker()` |

### Tagging System

- **Exam Tags:** JM, JA, CET, BITSAT (dropdown selector)
- **Subject Tags:** Physics, Chemistry, Mathematics, English, Logical Reasoning (dropdown selector)
- **Difficulty Tags:** E (Easy), M (Medium), T (Tough) (dropdown selector)
- **Question Type Tags:** SCQ, MCQ, NUM (dropdown selector)

### Folder Hierarchy

The save structure follows: `Exam/Subject/Difficulty/QuestionType/{filename}.png`

Example: `JM/Physics/M/SCQ/AbCd1234.png`

### Save Logic

1. Gets selected exam, subject, difficulty, and question type tags
2. Creates folder hierarchy: `{exam}/{subject}/{difficulty}/{qtype}/`
3. Generates random 8-char alphanumeric filename
4. Converts canvas to PNG blob
5. Writes to selected folder via File System Access API

### ID Generation

Uses `crypto.getRandomValues()` for cryptographically secure random filename generation with 8-character alphanumeric string.

---

## 2. UI Components

### Header Control Bar
- **Logo** - Animated SVG diamond with pulse-glow effect
- **Upload Button** - "📄 Upload PDF" file input
- **Folder Link** - "📁 Link Folder" button
- **Page Navigator** - Prev/Next buttons + page counter
- **Zoom Controls** - In/Out buttons + zoom percentage
- **Exam Dropdown** - Select exam type (JM/JA/CET/BITSAT)
- **Subject Dropdown** - Select subject (Physics/Chemistry/Mathematics/English/Logical Reasoning)
- **Difficulty Dropdown** - Select difficulty (E/M/T)
- **Question Type Dropdown** - Select type (SCQ/MCQ/NUM)
- **Add Part Button** - Append crop to preview
- **Reset Button** - Clear canvas
- **Save Button** - Export image (disabled until folder linked)

### Main Workspace
- **Left Panel** - PDF viewer with scroll, green glow border
- **Right Panel** - Preview sidebar showing cropped result

### Footer
- Copyright notice: "© 2026 ExamFillip. All rights reserved."
- Version info: "Question Sorting Engine V1.2"

### Interactive Elements
- Crop overlay appears on mouse drag (green glowing border)
- Buttons have hover effects with glow and slight lift
- Save button has special gradient styling (green)

---

## 3. Color Schemes

### CSS Variables (`:root`)

| Variable | Hex Code | Usage |
|----------|----------|-------|
| `--bg-base` | `#000000` | Main page background |
| `--bg-panel` | `rgba(20, 20, 20, 0.75)` | Control bar and sidebar panels |
| `--bg-border` | `rgba(255, 255, 255, 0.08)` | Subtle borders |
| `--text-main` | `#e5e7eb` | Primary text color |
| `--text-muted` | `#6b7280` | Secondary/muted text |
| `--accent-glow` | `#22c55e` | Primary accent (green) - buttons, crop overlay, success states |
| `--accent-warn` | `#facc15` | Warning/yellow - merge button |
| `--accent-danger` | `#ef4444` | Danger/red - clear button |

### Background
- Animated gradient: linear-gradient(-45deg, #000000, #0a0a0a, #000000, #111111)
- Animation: 18s ease infinite (bgMove keyframes)
- Size: 300% 300%

### Button Styles
- Default: `rgba(30, 30, 30, 0.8)` background
- Hover: `rgba(34, 197, 94, 0.12)` with green glow
- Save Button: `linear-gradient(135deg, #22c55e, #16a34a)` - special gradient

### Canvas Borders
- PDF wrapper: `rgba(34, 197, 94, 0.08)` subtle green glow
- Final canvas: `rgba(34, 197, 94, 0.2)` shadow

### Crop Overlay
- Border: 2px solid `#22c55e` (green)
- Background: `rgba(34, 197, 94, 0.08)`
- Box shadow: `0 0 15px rgba(34, 197, 94, 0.8)` strong glow

---

## 4. Key Implementation Details

### PDF.js Integration
- Library: `pdf.js 3.4.120` from cdnjs
- Worker: `pdf.worker.min.js` from same version
- Rendering: Canvas 2D context with viewport scaling

### File System Access API
- Uses `showDirectoryPicker()` for folder selection
- Creates directory handles for exam/difficulty hierarchy
- File handles with `createWritable()` for saving

### Canvas Operations
- tempCanvas: Hidden canvas for single crop
- finalCanvas: Visible canvas stacking multiple crops
- Stacking logic: Appends vertically with 10px gap, maintains max width

---

## 5. Development Notes

- All JavaScript inline in `<script>` tag
- No external CSS files (inline `<style>`)
- Requires browser with File System Access API support (Chrome/Edge)
- PDF.js worker loaded from CDN
- Responsive: flex/grid layout, flex-wrap on controls
- Animations: CSS keyframes for background and logo pulse

---

## 6. Answer Key Feature

### Answer Input Types
- **SCQ (Single Choice):** Dropdown with options 1, 2, 3, 4
- **MCQ (Multiple Correct):** Checkboxes to select multiple options (1,2,3,4)
- **NUM (Numerical):** Text input for integers or decimals (no fractions)

### Storage
- Answers stored in browser localStorage with structure:
  ```json
  {
    "questions": [
      { "id": "AbCd1234", "answer": "2", "exam": "JM", "subject": "Physics", "difficulty": "M", "qtype": "SCQ" }
    ]
  }
  ```

### Generate Answer Key
- "Generate Answer Key" button appears when answers are stored
- Creates `answer_key.txt` file in the exam folder
- Text format groups questions by folder structure

### UI Components Added
- Answer Type selector (synced with question type dropdown)
- SCQ dropdown, MCQ checkboxes, NUM input field
- "Clear Stored Answers" button
- "Generate Answer Key" button with count badge