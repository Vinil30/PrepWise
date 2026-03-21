# Elevate Interview

An AI-powered mock interview platform with real-time voice interaction, live chat, and automated performance reporting.

---

## Overview

Elevate Interview simulates a realistic job interview experience. Users enter their target role and job description, choose an interview type, and are connected to an AI interviewer (James Whitmore) who asks relevant questions in real time — via voice or text. At the end, the platform generates a detailed performance report with scores, strengths, and improvement areas.

---

## Pages & Files

| File | Route | Purpose |
|---|---|---|
| `index.html` | `/` | Job details form + interview type selection (2-step onboarding) |
| `interview.html` | `/interview` | Live interview UI — voice + text chat with AI interviewer |
| `report.html` | `/report` | Performance report — scores, analysis, strengths, conversation transcript |
| `error.html` | `/error` | Error fallback shown when report generation fails |

---

## Features

- **Two-step onboarding** — enter job role, paste job description, pick HR / Technical / Cultural round
- **Live interview chat** — real-time AI responses via `/ask` endpoint
- **Voice input** — Web Speech API with silence detection and auto-send after 4 seconds of quiet
- **Text-to-speech** — AI responses spoken aloud using Google UK English Male voice
- **Webcam preview** — candidate's camera shown during the interview
- **Session timer** — live elapsed time displayed on the interview page
- **End interview & report** — calls `/generate-report`, redirects to `/report` on success or `/error` on failure
- **Performance report** — overall score, accuracy %, confidence level, strengths, improvements, detailed analysis, full transcript

---

## Backend Endpoints (Flask)

| Method | Route | Description |
|---|---|---|
| `POST` | `/save-job-data` | Receives `jobRole`, `jobDescription`, `interviewType` as JSON; saves to session |
| `POST` | `/ask` | Receives `message` (form-encoded); returns `{ reply: "..." }` from AI |
| `POST` | `/generate-report` | Generates report from conversation history; returns `{ status: "success" }` or error |
| `GET` | `/interview` | Serves `interview.html` |
| `GET` | `/report` | Serves `report.html` with Jinja2 template variables |
| `GET` | `/error` | Serves `error.html` |

---

## Report Template Variables

`report.html` is a Jinja2 template. Your Flask route must pass:

```python
render_template(
    "report.html",
    report_score={
        "overall_score": 78,        # int, 0–100
        "accuracy": 82,             # int, 0–100
        "confidence": "High",       # "High" | "Medium" | "Low"
        "recommendation": "..."     # string
    },
    analysis="...",                 # string or "Not Available"
    strengths=["...", "..."],       # list of strings or ["Not Available"]
    improvements=["...", "..."],    # list of strings or ["Not Available"]
    conversation=[
        { "role": "user",      "content": "..." },
        { "role": "assistant", "content": "..." },
    ]
)
```

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | HTML, CSS, Vanilla JavaScript |
| Fonts | Plus Jakarta Sans (Google Fonts) |
| Voice Input | Web Speech API (`SpeechRecognition`) |
| Text-to-Speech | Web Speech Synthesis API |
| Templating | Jinja2 (via Flask) |
| Backend | Python / Flask |
| AI | Your LLM integration via `/ask` and `/generate-report` |

---

## Browser Support

Voice features (speech recognition + TTS) require a **Chromium-based browser** (Chrome, Edge, Brave). Firefox does not support the Web Speech API. All other UI features work across modern browsers.

---

## Project Structure

```
project/
├── templates/
│   ├── index.html        # Onboarding — step 1 & 2
│   ├── interview.html    # Live interview session
│   ├── report.html       # Post-interview report
│   └── error.html        # Error fallback
├── static/
│   └── ...             
├── app.py                # Flask application entry point
└── README.md
```

---

## Local Setup

```bash
# 1. Clone the repo
git clone https://github.com/your-username/elevate-interview.git
cd elevate-interview

# 2. Create and activate a virtual environment
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate

# 3. Install dependencies
pip install flask

# 4. Run the app
python app.py
```

Then open `http://localhost:5000` in Chrome.

---

## UI Theme

All pages share a unified dark navy design system:

- **Background** — `#0f1c2e` with blue/cyan radial gradient glows
- **Cards** — glass-morphism dark panels with `rgba(17,30,50,0.95)` and `1px` subtle borders
- **Font** — Plus Jakarta Sans (400 → 800 weight range)
- **Accent colors** — Blue `#2563eb`, Cyan `#06b6d4`, Muted text `#7a9abf`
- **Buttons** — blue gradient primary, red-tinted glass destructive (end interview)
- **Inputs** — resizable `<textarea>` with dark glass styling and blue focus ring
