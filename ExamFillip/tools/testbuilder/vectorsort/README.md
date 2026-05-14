# VectorSort - Question Sorter

A PDF-based question extraction and sorting tool for exam preparation. Extract questions from PDF question papers, crop specific questions, and save them organized by exam type, subject, difficulty level, and question type into a structured folder system.

![Version](https://img.shields.io/badge/version-1.2-blue)
![Platform](https://img.shields.io/badge/platform-Web-brightgreen)

---

## Features

### Core Functionality
- **PDF Upload & Rendering** - Load and view PDF files directly in the browser
- **Page Navigation** - Navigate between PDF pages with prev/next controls
- **Zoom Control** - Zoom in/out on PDF view (0.6x to 2.0x+)
- **Crop Selection** - Drag to select rectangular areas on the PDF
- **Multi-Part Crop** - Add multiple crops together before saving
- **Preview Panel** - Real-time preview of cropped questions

### Tagging System
- **Exam Type** - JM, JA, CET, BITSAT
- **Subject** - Physics, Chemistry, Mathematics, English, Logical Reasoning
- **Difficulty** - E (Easy), M (Medium), T (Tough)
- **Question Type** - SCQ (Single Choice), MCQ (Multiple Correct), NUM (Numerical)

### Answer Key Management
- **SCQ** - Select single answer (1, 2, 3, or 4)
- **MCQ** - Select multiple answers (checkboxes)
- **NUM** - Enter numerical answer (integer or decimal)
- **Generate Answer Key** - Export all stored answers to a text file
- **Clear Answers** - Reset all stored answer keys

### Folder Structure
Questions are saved with organized hierarchy:
```
Exam/Subject/Difficulty/QuestionType/{random_id}.png
```
Example: `JM/Physics/M/SCQ/AbCd1234.png`

---

## How to Use

### Step 1: Upload PDF
Click **"Upload PDF"** button to select a PDF question paper from your computer.

### Step 2: Link Folder
Click **"Link Folder"** to select the destination folder where questions will be saved. This creates the root folder for all saved questions.

### Step 3: Crop Questions
1. Navigate to the desired page using prev/next buttons
2. Adjust zoom level if needed
3. Click and drag on the PDF to select the question area
4. Use **"Add Part"** to add multiple selections together

### Step 4: Configure Tags
Select appropriate options from the dropdowns:
- Exam Type
- Subject
- Difficulty Level
- Question Type

### Step 5: Enter Answer
- For **SCQ**: Select answer from dropdown (1-4)
- For **MCQ**: Check multiple options
- For **NUM**: Type the numerical answer

### Step 6: Save Question
Click **"Save Question"** to export the cropped image to the folder structure. The question ID is randomly generated for unique identification.

### Step 7: Generate Answer Key
After saving multiple questions, click **"Generate Answer Key"** to export all stored answers to `answer_key.txt` in the exam folder.

---

## Browser Compatibility

VectorSort uses the **File System Access API** which is supported in:
- Google Chrome (90+)
- Microsoft Edge (90+)
- Opera (76+)

**Note:** Firefox and Safari do not fully support the File System Access API.

---

## Technology Stack

| Technology | Purpose |
|------------|---------|
| HTML5 | Page structure |
| CSS3 | Styling and animations |
| Vanilla JavaScript | Core functionality |
| PDF.js 3.4.120 | PDF rendering |
| File System Access API | File saving |

---

## Project Structure

```
vectorsort/
├── index.html      # Main application file (all-in-one)
├── README.md       # Documentation
└── logo.png        # Application logo
```

---

## Keyboard Shortcuts

| Action | Behavior |
|--------|----------|
| Drag on PDF | Select area to crop |
| Click "Add Part" | Stack current selection to preview |
| Click "Reset" | Clear all (canvas, PDF, folder, answers) |

---

## Privacy

- All processing happens locally in your browser
- No data is sent to any server
- Questions and answers are stored in browser localStorage
- Files are saved directly to your selected folder

---

## Version History

| Version | Changes |
|---------|---------|
| 1.2 | Answer Key feature added (SCQ/MCQ/NUM support) |
| 1.1 | Subject and Question Type tags added |
| 1.0 | Initial release with Exam and Difficulty tags |

---

## License

Copyright (c) 2026 ExamFillip. All rights reserved.

### You Can
- Use the application for personal/educational purposes
- View the code for learning

### You Cannot
- Copy or redistribute the code
- Use for commercial purposes
- Claim the code as your own

For any questions, feel free to contact the developer.

---

## Author

Created with dedication for exam preparation and question management.

For issues and feedback, please contact the developer.