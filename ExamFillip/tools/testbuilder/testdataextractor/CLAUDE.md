# ExamFillip Test Data Extractor

## Project Overview

This is a single-page HTML tool that extracts test data from exam question images and generates a structured JSON database. It's designed to work with a specific folder structure containing exam images organized by exam type, subject, difficulty, and question type.

## Core Functionality

The tool takes two inputs:
1. **Workspace Folder** - A folder containing `.png` question images organized in a specific hierarchy
2. **Answer Key File** - A `.txt` file containing question IDs and their correct answers

It outputs a `master_db.json` file with structured test data including question metadata, answers, and marking schemes.

## Expected Folder Structure

The workspace folder should follow this hierarchy:
```
/(exam)/(subject)/(difficulty)/(questionType)/(questionID).png

Example:
/JM/Physics/H/SCQ/ABCD1234.png
/JM/Physics/M/MCQ/EFGH5678.png
/JM/Chemistry/E/NUM/IJKL9012.png
```

Where:
- `exam` - Exam code (e.g., JM, NEET, CBSE)
- `subject` - Subject name (e.g., Physics, Chemistry, Math)
- `difficulty` - Difficulty level (E=Easy, M=Medium, H=Hard)
- `questionType` - Question type (SCQ, MCQ, NUM)
- `questionID` - 8-character alphanumeric identifier (e.g., ABCD1234)

## Answer Key Format

The answer key `.txt` file should have one answer per line in this format:
```
ABCD1234 - 1
EFGH5678 - 3
IJKL9012 - 2
```

Format: `<8-char-question-id><space>-<space><answer-digit>`

## Input Elements

| Element | ID | Purpose |
|---------|-----|---------|
| Folder Input | `folderInput` | Select workspace folder with images |
| Answer Key Input | `ansInput` | Select .txt answer key file |
| SCQ Positive | `scqPos` | Positive marks for SCQ (default: 4) |
| SCQ Negative | `scqNeg` | Negative marks for SCQ (default: 1) |
| MCQ Positive | `mcqPos` | Positive marks for MCQ (default: 4) |
| MCQ Negative | `mcqNeg` | Negative marks for MCQ (default: 2) |
| NUM Positive | `numPos` | Positive marks for NUM (default: 4) |
| NUM Negative | `numNeg` | Negative marks for NUM (default: 0) |

## Output JSON Structure

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

## JavaScript Functions

### `toggleTheme()`
Toggles between dark and light theme. Stores preference in `localStorage` under key `theme`.

### `generateJSON()` (Main function)
Async function that:
1. Reads selected folder for `.png` files
2. Parses folder path to extract exam, subject, difficulty, question type
3. Reads answer key file and matches question IDs
4. Applies marking scheme based on question type
5. Generates and downloads `master_db.json`

### `animateShake(element)`
Triggers shake animation on element (used for validation errors)

## CSS Variables (Theme System)

### Dark Theme (default)
```css
--primary: #6366f1
--accent: #10b981
--bg: #0f172a
--card-bg: #1e293b
--text: #f8fafc
--border: #334155
```

### Light Theme
```css
--bg: #f1f5f9
--card-bg: #ffffff
--text: #1e293b
--border: #e2e8f0
```

Theme is controlled via `data-theme` attribute on `<html>` element.

## UI Components

- **Theme Toggle** - Animated toggle switch in header (sun/moon icons)
- **File Drop Zones** - Custom styled clickable areas for file selection with visual feedback
- **Marking Scheme Grid** - Three cards (SCQ, MCQ, NUM) with positive/negative inputs
- **Extract Button** - Gradient button with shimmer hover effect
- **Status Message** - Animated success/error messages
- **Footer** - Copyright and version info

## Animations

- `fadeSlideUp` - Page load animations with staggered delays
- `pulse` - Button loading state
- `shimmer` - Button hover effect (light sweep)
- `successPulse` - Success message animation
- `shake` - Validation error animation

## How to Modify

### Adding New Question Types
1. Add new card in HTML marking-grid section
2. Add input fields with unique IDs (e.g., `truePos`, `trueNeg`)
3. Update `marks` object in `generateJSON()` function

### Changing Output Format
Modify the `masterData[qid]` object construction in `generateJSON()` function (lines 632-644).

### Adding New Themes
Add new `[data-theme="themeName"]` selector in CSS with updated CSS variables.

### Modifying File Validation
Change the regex in `generateJSON()` at line 623:
```javascript
const match = line.match(/([A-Za-z0-9]{8})\s*-\s*(\d+)/);
```
The regex expects: 8 alphanumeric chars, dash, digit.

## Browser Compatibility

- Uses `webkitdirectory` attribute for folder selection (Chrome/Edge/Safari)
- Uses `File API` for file reading
- Uses `localStorage` for theme persistence
- No external dependencies - pure vanilla HTML/CSS/JS