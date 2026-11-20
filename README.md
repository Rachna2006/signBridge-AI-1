<div align="center">

# SignBridge AI

**Breaking Silence. Connecting People.**

An AI-powered Indian Sign Language (ISL) communication prototype: webcam sign recognition, speech tools and emergency phrases in a light, accessible web app.

`React` · `Vite` · `Flask` · `SQLite` · `MediaPipe` · `Python`

</div>

> **Project status: early prototype (Stage 1 of 9).** The web app foundation is working. The sign-recognition model is **not trained yet**, so the app does not recognize signs today. Nothing in this repository fakes AI results.

<!-- Add a screenshot after you run the app:
![SignBridge AI landing page](docs/screenshot-landing.png) -->

**Live demo (static, no server):** _add your GitHub Pages URL here_ (see [Static demo](#static-demo-github-pages))

**Full app (React + Flask):** _add your Render URL here after deploying_ (see [README_RENDER.md](README_RENDER.md))

---

## Table of contents

1. [Problem and idea](#problem-and-idea)
2. [Features and status](#features-and-status)
3. [How it will work](#how-it-will-work)
4. [Tech stack](#tech-stack)
5. [Getting started](#getting-started)
6. [Static demo (GitHub Pages)](#static-demo-github-pages)
7. [Project structure](#project-structure)
8. [API](#api)
9. [Roadmap](#roadmap)
10. [Limitations and responsible use](#limitations-and-responsible-use)
11. [Privacy](#privacy)
12. [Datasets and credits](#datasets-and-credits)
13. [Contributing and license](#contributing-and-license)

---

## Problem and idea

Many deaf and hard-of-hearing people use sign language, but most people they meet in daily life do not. An interpreter is not always available, for example at a clinic desk or a public service counter.

SignBridge AI explores a laptop-only solution: recognize a **small, defined vocabulary** of ISL signs from a webcam, show them as text, speak them aloud, and let the other person reply by voice, which is shown as text.

Intended use cases (not existing deployments): everyday conversations, clinics and hospitals, schools and colleges, public service centres.

## Features and status

| Feature | Status |
|---|---|
| Responsive pastel-blue UI, landing page, collapsible dashboard | Done |
| Translation history (search, delete one, clear all) with SQLite | Done |
| Flask API with health check | Done |
| Render deployment files | Done |
| Static single-file demo for GitHub Pages | Done |
| Sign data collection tool | Planned (Stage 2) |
| Model training on a 12-15 sign vocabulary | Planned (Stage 3) |
| Recognition API and live webcam translator | Planned (Stages 4-5) |
| Text-to-speech output | Planned (Stage 5) |
| Speech-to-text | Planned (Stage 6) |
| Emergency quick phrases (spoken, large text) | Planned (Stage 7) |
| Learn ISL lessons with progress | Planned (Stage 8) |
| About, Settings, final polish | Planned (Stage 9) |

Pages for planned features show a clear "Not built yet" message instead of fake output.

## How it will work

```
Webcam -> MediaPipe Hands (in the browser) -> hand landmarks
      -> small landmark arrays sent to Flask API
      -> trained gesture model -> recognized sign + real confidence score
      -> text on screen + spoken with browser speech synthesis
```

- **MediaPipe runs in the browser.** Only landmark numbers, never video, are sent to the server. This keeps the camera smooth on a normal laptop and protects privacy.
- **Two hands, sequences of frames.** Many ISL signs use both hands and movement, so the model will take a short sequence of two-hand landmarks.
- **Honest confidence.** Confidence is shown only when the real model computes it. Unknown or low-confidence gestures are reported as such.

## Tech stack

| Layer | Technology |
|---|---|
| Frontend | React 18, Vite, React Router, Lucide icons, plain CSS |
| Backend | Python 3.10/3.11, Flask, Flask-CORS, Gunicorn |
| Database | SQLite (prototype) |
| Computer vision (planned) | MediaPipe Hands, OpenCV for data preparation |
| Machine learning (planned) | TensorFlow/Keras or scikit-learn |
| Speech (planned) | Browser SpeechSynthesis, browser or Whisper speech recognition |
| Hosting | Render (static site + web service) |

## Getting started

### Requirements

- Python 3.10 or 3.11 (64-bit)
- Node.js 20 LTS
- Git

### 1. Clone

```bash
git clone https://github.com/YOUR-USERNAME/signbridge-ai.git
cd signbridge-ai
```

### 2. Run the backend (port 5000)

Windows (PowerShell):
```powershell
cd backend
python -m venv venv
venv\Scripts\activate
pip install -r requirements.txt
python app.py
```

macOS / Linux:
```bash
cd backend
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
python app.py
```

If PowerShell blocks `activate`, run once: `Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned`

### 3. Run the frontend (port 5173), in a second terminal

```bash
cd frontend
npm install
npm run dev
```

Open **http://localhost:5173** in Chrome or Edge.

### 4. Check that it works

1. The landing page loads.
2. **Get Started** opens the dashboard, and the badge says **Backend: Online**.
3. On **Translation History**, add a developer test record, search for it, delete it.

### Troubleshooting

| Problem | Fix |
|---|---|
| Badge says Backend: Offline | Start the backend and check for errors in its terminal. |
| `python` or `npm` not recognized | Install it and reopen the terminal (tick "Add Python to PATH"). |
| Port already in use | Close the other program, or change the port in `backend/app.py` and `frontend/vite.config.js`. |
| `npm install` fails | Delete `node_modules` and `package-lock.json`, then retry. |
| Blank page | Open the browser console (F12) and read the red error. |

### Deploy to Render

Full guide: [README_RENDER.md](README_RENDER.md). The repository includes a `render.yaml` Blueprint.

## Static demo (GitHub Pages)

`docs/index.html` is a single-file version of the website that needs no install and no backend. It has a working camera preview, speech-to-text (Chrome or Edge), spoken emergency phrases and a browser-only history. It does **not** recognize signs yet.

To get a free public link:

1. Push this repository to GitHub.
2. Open **Settings**, then **Pages**.
3. Under "Build and deployment" choose **Deploy from a branch**, select branch `main` and folder `/docs`, then **Save**.
4. After a minute or two the site is live at `https://YOUR-USERNAME.github.io/REPOSITORY-NAME/`.

To try it locally, double-click `docs/index.html`.

## Project structure

```
signbridge-ai/
├── backend/
│   ├── app.py                 Flask app, CORS, health check
│   ├── db.py                  SQLite setup
│   ├── routes/history.py      History endpoints
│   └── requirements.txt
├── frontend/
│   ├── index.html
│   ├── package.json
│   ├── vite.config.js         Dev server and /api proxy
│   └── src/
│       ├── main.jsx, App.jsx
│       ├── components/AppShell.jsx
│       ├── pages/             Landing, Dashboard, History, ComingSoon
│       ├── services/api.js    All backend calls
│       └── styles/theme.css
├── docs/
│   └── index.html             Static demo for GitHub Pages
├── dataset/                   Sign data (Stage 2)
├── training/                  Training scripts (Stage 3)
├── render.yaml                Render Blueprint
├── README.md
└── README_RENDER.md
```

## API

| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/health` | Service status (`model_loaded` is `false` until a model is added) |
| GET | `/api/history/?q=` | List history, optional text search |
| POST | `/api/history/` | Add a record: `text`, `kind`, optional `gesture` |
| DELETE | `/api/history/<id>` | Delete one record |
| DELETE | `/api/history/` | Clear all history |

`kind` is one of `sign_to_text`, `speech_to_text`, `emergency`, `test`.

## Roadmap

- [x] Stage 1: foundation (UI, history, backend, deployment files)
- [ ] Stage 2: choose and verify signs, build data-collection tool
- [ ] Stage 3: preprocess data and train the model
- [ ] Stage 4: backend recognition API
- [ ] Stage 5: live webcam translator and text-to-speech
- [ ] Stage 6: speech-to-text
- [ ] Stage 7: emergency mode
- [ ] Stage 8: Learn ISL
- [ ] Stage 9: About, Settings, polish

Future scope: more signs, continuous sign recognition, facial expressions, sentence-level translation, regional languages, offline mode, mobile app.

## Limitations and responsible use

- This is a prototype. It will recognize **only the signs it is trained on**. It does not translate full Indian Sign Language: continuous signing, grammar and facial expressions are not implemented.
- ISL varies by region and signer. Accuracy on new people and cameras must be tested, not assumed.
- Signs used in the app must be checked against trusted ISL references (for example the ISLRTC dictionary) and, ideally, a fluent signer or deaf-education teacher.
- **Do not rely on this app for medical, legal or emergency communication.** The emergency page only shows and speaks phrases. It does not contact emergency services.

## Privacy

- Webcam video and audio are not recorded or stored.
- Only text results (for example a recognized word, its type and a timestamp) are saved in the local SQLite database.
- Users can delete one record or clear all history.

## Datasets and credits

- The plan is to build the vocabulary using **INCLUDE**, a large-scale ISL dataset by AI4Bharat and IIT Madras, plus our own recordings. Check the dataset's license and cite its paper before using it. Dataset files are **not** included in this repository.
- Hand tracking by [MediaPipe](https://developers.google.com/mediapipe).
- Icons by [Lucide](https://lucide.dev).

## Contributing and license

Issues and suggestions are welcome, especially from ISL users and educators.

**License:** no license has been chosen yet. Add a `LICENSE` file before publishing (the MIT License is a common choice for hackathon projects). Check any dataset's license separately.
