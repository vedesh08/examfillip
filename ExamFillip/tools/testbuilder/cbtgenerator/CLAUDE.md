JEE Master Ecosystem: Technical Architecture & Logic

This document serves as the comprehensive technical blueprint for the CBT Portal Generator. It details the UI implementation, state management, and the specific JavaScript logic used to simulate the TCS iON/NTA examination environment.

1. Core Architecture

The system operates as a Single-Page Application (SPA) using a "View Switching" architecture.

View 1: #admin-panel -> Entry point for JSON ingestion and configuration.
- Upload JSON question bank (accepts .json files)
- Configure exam name and duration
- View content analysis (subject-wise breakdown with SCQ/MCQ/NUM counts)
- Launch preview or generate standalone HTML

View 2: #portal-view -> The exam terminal.
- Header with timer countdown and candidate name
- Subject-wise navigation via horizontal slabs
- Question display area with image support
- Options grid (SCQ/MCQ/NUM handling)
- Question palette sidebar showing status with live question counts
- Mark for Review, Clear Response, Save & Next buttons

View 3: #report-view -> Post-exam analytics.
- Total score, correct, incorrect, accuracy percentages
- Subject-wise breakdown table
- Restart and download results options


2. Data Structure (The Question Object)

The system expects an array or object of questions. Each question must follow this schema:

{
    "qid": "UniqueString",
    "image": "Optional URL to question image",
    "text": "Optional plain text question",
    "subject": "Physics | Chemistry | Maths",
    "type": "SCQ | MCQ | NUM",
    "answer": "1" (SCQ), "1,2,3" (MCQ), "25.5" (NUM),
    "marking": { "positive": 4, "negative": -1 }
}

Note: JSON can be either an array or a Map-like object structure (Object.values() is used to normalize).


3. JavaScript Logic Engines

A. State Management

The application's "truth" is held in these global variables:

allQ: An array of all processed question objects.

subjects: Array of unique subject names extracted from questions.

userResponses: A dictionary mapping qid to an object:
{
  ans: "selected_value", // String for SCQ/NUM, Array for MCQ, null if unanswered
  status: "unvisited | not-answered | answered | marked | marked-answered"
}

curIdx: Current question index in allQ.

activeSub: Currently selected subject for palette filtering.

timeRem: Remaining time in seconds.

timerInt: setInterval reference for countdown.


B. The Evaluation Engine (evalExam)

Scoring algorithm handles each question type:

SCQ & NUM: Straightforward string comparison: userAns.trim() === correctAns.trim().

MCQ Logic (with partial marking):
- Split correct answers by comma into array
- Check if any user-selected option is NOT in correct answers -> apply negative marking
- If all selected are correct:
  - If count(selected) === count(correct): full positive marks
  - If count(selected) < count(correct): +1 mark per correct selected option (partial)


C. The Standalone Exporter (exportStandalone)

This function performs a "Code Injection":
- Embeds portal-view, report-view, submit-modal HTML
- Injects allQ as JSON and subjects array
- Includes all required functions (buildSlabs, loadQ, renderOptions, etc.)
- The exported file requires zero external dependencies except CDN links for Tailwind and Font Awesome


4. UI/UX Implementation (JEE Fidelity)

A. The Palette Geometry

JEE-style shapes achieved via CSS clip-path:

Answered (Green): polygon(0% 0%, 100% 0%, 100% 60%, 85% 100%, 15% 100%, 0% 60%)

Not Answered (Red): polygon(15% 0%, 85% 0%, 100% 40%, 100% 100%, 0% 100%, 0% 40%)

Marked (Purple): border-radius: 50%

Marked & Answered: Purple circle with green checkmark overlay (::after pseudo-element)


B. Navigation Logic

loadQ(index): Primary router. Updates curIdx, fetches from allQ, triggers renderOptions() and renderPalette().

Subject Slabs: Filter allQ by subject, render palette showing only questions for that subject.

Palette groups: Questions grouped by type within each subject: SCQ Section, MCQ Section, NUM Section.


C. Key Functions

- processJSON: Reads JSON file, normalizes to array via Array.isArray() or Object.values()
- analyzeData: Extracts subjects, calculates stats per subject-type, initializes userResponses
- launchPortal: Switches to portal view, initializes timer, loads first question
- renderOptions: Renders based on type (radio for SCQ, checkboxes for MCQ, number input for NUM)
- renderPalette: Groups by subject, then by type, applies CSS classes based on status
- updateStatusCounts: Updates live counts for all question statuses in the sidebar
- saveNext: Updates status based on answer presence, moves to next question
- markReview: Updates status to marked/marked-answered, moves to next
- clearResponse: Resets ans to null and status to not-answered
- downloadResults: Exports userResponses as JSON file


5. Persistence

downloadResults() exports userResponses as JSON file for external record-keeping.

Note: The original CLAUDE.md mentioned window.onbeforeunload localStorage sync, but this is NOT implemented in the current code.


6. Future Expansion Points

Matrix Match Type: Requires new renderOptions case and specific scoring loop within evalExam.

Image Zoom: JEE portals often include magnifying glass feature for high-res diagrams.

Integer Type: Currently NUM accepts decimals via step="any" - could add separate type for integer-only.