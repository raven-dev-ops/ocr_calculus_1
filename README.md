# Accessible Calculus Notes OCR Project

## Project Overview
This project delivers an OCR + AI pipeline that converts uploaded PDFs/images of handwritten notes into accessible digital text (MathML/LaTeX) and incrementally approaches ~99% accuracy through guided feedback. The pilot deployment targets Calculus I at Johnson County Community College (JCCC) in Kansas City, MO.

## Objectives
- **Accessible Notes:** Provide screen-reader-friendly calculus notes (text + MathML/LaTeX) instead of inaccessible images.  
- **High Accuracy (~99%):** Combine Tesseract with math-aware OCR (e.g., Pix2Text) and AI correction to exceed ~80% baseline accuracy.  
- **Continuous Learning:** Use course materials as a knowledge base to correct OCR errors and improve over ~90 days.  
- **User-Friendly Frontend:** React UI for instructors to upload/review and for students to consume accessible notes.  
- **Admin & Support:** Roles, monitoring, ticketing, and email alerts for issues.  
- **CI/CD:** Auto-deploy frontend (Netlify) and backend (Heroku) on repo updates.

## OCR Processing Pipeline
1) **Upload & Preprocess:** Contrast adjustment, scaling, and de-noising to isolate text/math.  
2) **Text Extraction:** PyTesseract configured for English and layout nuances.  
3) **Math Recognition:** Specialized math OCR (e.g., Pix2Text) converts handwritten formulas to LaTeX/Markdown.  
4) **Combine Outputs:** Produce structured Markdown/LaTeX or HTML/MathML preserving headings, lists, and numbering.  
5) **Return + Store:** Persist processed content and surface confidence/flags for review.

## AI Error Correction & Continuous Improvement
- **Knowledge Base:** Course textbook, typed notes, slides, syllabi indexed for verification.  
- **Inference of Missing Content:** Compare OCR output to references to flag/auto-fill mistranscribed or omitted sections.  
- **Feedback Loop:** Log low-confidence spans; instructors review side-by-side image/text; corrections stored.  
- **Model Updates:** Heuristics and potential fine-tuning on recurring handwriting patterns; iterative gains toward ~99% accuracy.  
- **Confidence & Review:** Low-confidence content flagged for human review; manual effort drops as model improves.

## Frontend (React on Netlify)
- **Instructor:** Authenticated upload (PDF/images), course/session tagging, review of OCR output, math rendering (MathJax/KaTeX), flagged sections highlighted, inline edits, publish/download accessible notes.  
- **Student:** Authenticated access to published notes; semantic HTML with MathML/LaTeX for screen readers; search and navigation by heading/page; simple issue flagging.  
- **Admin:** Role-gated views within same app; manages users/courses/materials; monitors processing status and metrics; views tickets.  
- **Accessibility:** WCAG-minded—keyboard navigation, ARIA labels, high contrast, responsive layout, screen-reader-friendly alerts.

## Backend (Python API on Heroku)
- **Framework:** Flask/FastAPI REST API.  
- **Endpoints:** `POST /process_notes` for uploads → preprocess → OCR → AI correction → store/return text + confidence flags. Ticket CRUD and admin controls.  
- **Data Storage:** PostgreSQL for users/roles, courses, processed notes, tickets, feedback logs; optional search index/vector store for course materials.  
- **AI Module:** Text search + rule-based/LLM-assisted comparison against knowledge base; optional async/background processing for heavy jobs.  
- **Notifications:** Email via SendGrid/SMTP when tickets/issues are created or processing fails.  
- **Security:** AuthZ by role; private storage for uploads/results; HTTPS; env-based secrets.

## Admin Dashboard & Ticketing
- **Dashboard:** Recent uploads, accuracy/confidence metrics, outstanding flags/tickets.  
- **User/Course Management:** Create courses, assign instructors, manage materials.  
- **Review Tools:** Inspect processed notes, flagged spans, and corrections.  
- **Ticketing:** Student/instructor issue reports; status updates and assignments; email alerts; optional summary digests.

## Accessibility Focus
- **Content Output:** Structured text + MathML/LaTeX for screen readers/braille tools; avoid image-only math.  
- **Screen Reader Testing:** Validate with NVDA/JAWS/VoiceOver for reading order and math speech.  
- **Keyboard-Only UX:** Proper elements, focus order, and operable uploads/actions.  
- **Contrast & Scaling:** High-contrast, zoom-friendly UI.  
- **No Surprises:** Inform users of long-running tasks; avoid timing constraints.

## Deployment & Repo Structure (Monorepo)
```
.
├── frontend/           # React app (Netlify)
│   ├── public/
│   ├── src/
│   └── package.json
├── backend/            # Python API (Heroku)
│   ├── app.py or main.py
│   ├── requirements.txt
│   ├── OCR/            # Tesseract, Pix2Text integration
│   ├── AI/             # Knowledge-base comparison, heuristics/LLM hooks
│   ├── models/
│   └── database/
├── data/               # git-ignored uploads/reference artifacts
├── docs/               # design/operational docs
├── .github/workflows/  # CI/CD configs (tests + deploy triggers)
└── README.md
```
- **CI/CD:** On push, run tests; deploy frontend to Netlify and backend to Heroku (Procfile/Dockerfile).  
- **Environments:** Recommend staging + production; configure secrets via platform env vars.

## Phased Delivery
- **Phase 1 (MVP):** End-to-end OCR pipeline, accessible output, instructor review/publish, basic admin, Calculus I rollout, screen reader validation.  
- **Phase 2 (90-day refinement):** Incorporate feedback, handwriting pattern fixes, knowledge base expansion, stronger ticketing/admin tools, incremental accuracy boosts.  
- **Phase 3 (scale):** Multi-course support, school SSO, advanced AI/tutoring, mobile/live capture, analytics/reporting.

## Key Sources
- Making Math Accessible – Rutgers ADR: https://radr.rutgers.edu/resource/making-math-accessible  
- PyTesseract Guide & limits (~80% baseline): https://www.extend.ai/resources/pytesseract-guide-ocr-limits-alternatives  
- Pix2Text math/text OCR: https://github.com/breezedeus/Pix2Text  
- OCR accuracy verification: https://verityai.co/blog/ocr-accuracy-verification-ai-menu-content  
- OCR benchmarks: https://www.vao.world/blogs/ocr-accuracy-benchmarks-the-2025-digital-transformation-revolution
