# ExamFillip Test Data Extractor

A single-page HTML tool that extracts test data from exam question images and generates a structured JSON database.

## Features

- **Easy Setup** - Just open `index.html` in your browser, no installation required
- **Folder Structure Support** - Organize exam images by exam, subject, difficulty, and question type
- **Customizable Marking Scheme** - Configure positive/negative marks for SCQ, MCQ, and NUM question types
- **Dark/Light Theme** - Toggle between themes with preference saved locally
- **Animated UI** - Smooth animations and visual feedback
- **No Dependencies** - Pure vanilla HTML/CSS/JavaScript

## How to Use

### 1. Prepare Your Folder Structure

Organize your `.png` question images in this hierarchy:
```
/(exam)/(subject)/(difficulty)/(questionType)/(questionID).png

Example:
/JM/Physics/H/SCQ/ABCD1234.png
/JM/Physics/M/MCQ/EFGH5678.png
/JM/Chemistry/E/NUM/IJKL9012.png
```

**Path Components:**
| Component | Description | Example |
|-----------|-------------|---------|
| exam | Exam code | JM, NEET, CBSE |
| subject | Subject name | Physics, Chemistry, Math |
| difficulty | Difficulty level | E (Easy), M (Medium), H (Hard) |
| questionType | Question type | SCQ, MCQ, NUM |
| questionID | 8-char unique ID | ABCD1234 |

### 2. Create Answer Key File

Create a `.txt` file with one answer per line:
```
ABCD1234 - 1
EFGH5678 - 3
IJKL9012 - 2
```

Format: `<8-char-question-id> - <answer-digit>`

### 3. Run the Tool

1. Open `index.html` in a browser (Chrome, Edge, or Safari recommended)
2. Click the folder drop zone to select your workspace folder
3. Click the answer key drop zone to select your `.txt` file
4. Optionally adjust marking scheme values
5. Click "Extract Test Data in .json file"
6. The `master_db.json` file will be downloaded automatically

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

## Default Marking Scheme

| Question Type | Positive Marks | Negative Marks |
|---------------|----------------|----------------|
| SCQ | 4 | 1 |
| MCQ | 4 | 2 |
| NUM | 4 | 0 |

You can customize these values before extracting.

## Browser Compatibility

- **Chrome** - Full support
- **Edge** - Full support
- **Safari** - Full support

**Note:** The folder selection feature uses `webkitdirectory` attribute, which is supported in Chromium-based browsers and Safari.

## Tech Stack

- HTML5
- CSS3 (CSS Variables, Animations, Flexbox/Grid)
- Vanilla JavaScript (File API, localStorage)
- Google Fonts (Inter)

---

## License

Copyright (c) 2026 [ExamFillip](https://examfillip.vercel.app/). All rights reserved.

**You Can**

- Use the application for personal/educational purposes
- View the code for learning

**You Cannot**

- Copy or redistribute the code
- Use for commercial purposes
- Claim the code as your own

For any questions, feel free to contact the developer.

---

## Version

v1.1 - Test Data Extractor