# MedRoute

> A Geospatially Optimized Smart Pharmaceutical Redistribution Network — connecting medicine donors with verified recipients through AI-powered verification, real-time geospatial matching, and multilingual accessibility.

**Live Demo →** [https://minipro-alpha.vercel.app/](https://minipro-alpha.vercel.app/)  
**Source Code →** [https://github.com/hkkkkkkkk-19/minipro](https://github.com/hkkkkkkkk-19/minipro)

---

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [System Architecture](#system-architecture)
- [Tech Stack](#tech-stack)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Environment Variables](#environment-variables)
- [Running Locally](#running-locally)
- [Deployment](#deployment)
- [Project Structure](#project-structure)
- [Academic Information](#academic-information)

---

## Overview

India discards medicines worth over ₹2000 crores annually while millions lack access to essential medicines. MedRoute addresses this by providing a structured, verified, and intelligent platform that connects medicine donors with those who need them most.

The platform serves four user roles across a unified redistribution pipeline:

- **Donor** — households, clinics, or pharmacies donating surplus medicines
- **Receiver** — verified patients requesting medicines from the redistribution inventory
- **NGO / Volunteer** — managing collection and delivery logistics
- **Government** — real-time monitoring, oversight, and emergency routing

---

## Features

- **OCR-based medicine verification** — YOLOv8n pill detection + EasyOCR text extraction from blister pack images, with rule-based expiry classification (Expired / Urgent / Expiring Soon / Within a Year / Safe)
- **Geospatial donor-recipient matching** — Haversine nearest-neighbor matching + Google Maps API with Dijkstra's algorithm for optimized delivery routing
- **ABHA-based identity verification** — Ayushman Bharat Health Account authentication for receivers, with doctor-approved prescription validation
- **Multilingual conversational interface** — Sarvam AI text and voice interaction across Indian languages for rural and low-literacy users
- **Real-time delivery tracking** — Firestore live listeners propagate status updates across all dashboards instantly
- **Disaster-aware government dashboard** — geographic heat maps, emergency zone flagging, and AI-assisted priority routing with government confirmation

---

## System Architecture

MedRoute follows a three-layer client-server architecture:

| Layer | Components |
|---|---|
| Presentation | React.js frontend (Vercel) — role-based routing for all four modules |
| Application & Logic | Firebase (Auth, Firestore, Cloud Functions) + Python ML microservice (Render) |
| Data & Services | Firestore database, Google Maps API, Sarvam AI |

The Python ML microservice runs separately from Firebase because YOLOv8n inference exceeds Firebase Cloud Function memory and execution time limits. It is deployed on Render with the model kept loaded between requests to eliminate cold-start latency.

---

## Tech Stack

| Component | Technology |
|---|---|
| Frontend | React.js |
| Frontend Deployment | Vercel |
| Backend Database | Firebase Firestore |
| Backend Auth | Firebase Authentication |
| Backend Logic | Firebase Cloud Functions |
| ML Microservice | Python, Flask |
| ML Microservice Deployment | Render |
| Pill Detection | YOLOv8n (Ultralytics) |
| Text Extraction | EasyOCR |
| Geospatial Routing | Google Maps API + Dijkstra's Algorithm |
| Distance Calculation | Haversine Formula |
| Multilingual Interface | Sarvam AI |
| Version Control | GitHub |

---

## Prerequisites

Make sure the following are installed before proceeding:

- [Node.js](https://nodejs.org/) v18 or later (with npm)
- [Python](https://www.python.org/) 3.9 or later (with pip)
- [Git](https://git-scm.com/)
- A [Firebase](https://firebase.google.com/) project with Firestore and Authentication enabled
- A [Google Maps API key](https://developers.google.com/maps) with Maps JavaScript, Directions, and Geocoding APIs enabled
- A [Sarvam AI API key](https://www.sarvam.ai/)

Verify your setup:

```bash
node -v
npm -v
python --version
git --version
```

---

## Installation

### Step 1 — Clone the Repository

```bash
git clone https://github.com/hkkkkkkkk-19/minipro.git
cd minipro
```

### Step 2 — Install Frontend Dependencies

```bash
npm install
```

### Step 3 — Install ML Microservice Dependencies

```bash
cd ml-service
pip install -r requirements.txt
```

The `requirements.txt` includes: `ultralytics`, `easyocr`, `opencv-python`, `flask`, and related packages.

---

## Environment Variables

### Frontend — `.env` (root directory)

Create a `.env` file in the project root:

```bash
touch .env
```

Add the following:

```env
REACT_APP_FIREBASE_API_KEY=your_firebase_api_key
REACT_APP_FIREBASE_AUTH_DOMAIN=your_project.firebaseapp.com
REACT_APP_FIREBASE_PROJECT_ID=your_firebase_project_id
REACT_APP_FIREBASE_STORAGE_BUCKET=your_project.appspot.com
REACT_APP_FIREBASE_MESSAGING_SENDER_ID=your_sender_id
REACT_APP_FIREBASE_APP_ID=your_app_id
REACT_APP_GOOGLE_MAPS_API_KEY=your_google_maps_api_key
REACT_APP_SARVAM_API_KEY=your_sarvam_api_key
REACT_APP_ML_SERVICE_URL=http://localhost:5001
```

### ML Microservice — `ml-service/.env`

```bash
touch ml-service/.env
```

```env
FLASK_PORT=5001
MODEL_PATH=model/yolov8n_pills.pt
```

A `.env.example` file is included in the repository with all required keys listed.

> **Never commit `.env` files.** They are included in `.gitignore` by default.

---

## Running Locally

Open two terminal windows.

**Terminal 1 — Start the ML microservice:**

```bash
cd ml-service
python app.py
```

The Flask server will start at `http://localhost:5001`. The YOLOv8n model loads once and stays in memory between requests.

**Terminal 2 — Start the React frontend:**

```bash
cd ..
npm start
```

The React app will open at `http://localhost:3000`.

| Service | URL |
|---|---|
| Frontend | http://localhost:3000 |
| ML Microservice | http://localhost:5001 |

Ensure `REACT_APP_ML_SERVICE_URL` in your `.env` points to `http://localhost:5001` for local end-to-end testing.

---

## Deployment

### Frontend — Vercel

**Via GitHub (recommended):**

1. Push the repository to GitHub.
2. Go to [vercel.com](https://vercel.com/) → **New Project** → Import your repository.
3. Set the **Root Directory** to the project root (where `package.json` lives).
4. Add all `REACT_APP_*` environment variables under **Project Settings → Environment Variables**.
5. Click **Deploy**.

Vercel automatically redeploys on every push to `main`.

**Via Vercel CLI:**

```bash
npm install -g vercel
vercel --prod
```

---

### ML Microservice — Render

1. Go to [render.com](https://render.com/) → **New → Web Service**.
2. Connect your GitHub repository and set the **Root Directory** to `ml-service`.
3. Set the **Build Command:** `pip install -r requirements.txt`
4. Set the **Start Command:** `python app.py`
5. Set the **Instance Type** to at least 512 MB RAM (YOLOv8n requires memory to stay loaded).
6. Add environment variables (`MODEL_PATH`, etc.) under **Environment**.
7. Click **Create Web Service**.

After deploying, copy the Render service URL and update `REACT_APP_ML_SERVICE_URL` in your Vercel environment variables to point to it.

---

### Firebase Setup

1. Go to [console.firebase.google.com](https://console.firebase.google.com/) and create a project.
2. Enable **Firestore Database** (start in production mode).
3. Enable **Firebase Authentication** (Email/Password provider).
4. Enable **Firebase Storage** (for prescription image uploads).
5. Copy your Firebase config keys into the frontend `.env`.
6. Deploy Firestore security rules from the repository's `firestore.rules` file.

---

## Project Structure

```
minipro/
├── src/
│   ├── pages/               # Role-based route modules
│   │   ├── donor/           # /donor — medicine submission and OCR verification
│   │   ├── receiver/        # /receiver — ABHA auth, Sarvam AI interface, requests
│   │   ├── ngo/             # /ngo — delivery assignment and status updates
│   │   └── government/      # /government — monitoring dashboard and emergency routing
│   ├── components/          # Shared UI: map, form, dashboard elements
│   ├── firebase/            # Firebase config, auth context, Firestore utilities
│   └── utils/               # Haversine formula, expiry classification, helpers
│
├── ml-service/
│   ├── app.py               # Flask REST API — /verify endpoint
│   ├── model/               # Trained YOLOv8n weights and inference utilities
│   ├── ocr/                 # EasyOCR extraction, preprocessing pipeline, regex parsing
│   └── requirements.txt     # Python dependencies
│
├── .env.example             # Template for all required environment variables
├── firestore.rules          # Firestore security rules
└── package.json
```

---

## Academic Information

| Field | Detail |
|---|---|
| Project Type | Mini Project |
| Semester | 6th Semester |
| Purpose | Academic learning and practical implementation of software engineering concepts |
| ML Model | YOLOv8n — mAP@0.5: 0.9938, Test Accuracy: 0.99, AUC: 0.9907 |
| OCR Pipeline | EasyOCR — Precision: 0.9950, Recall: 0.9800, Expired Medicine Recall: 1.00 |
| Geospatial Matching | 65.8% reduction in delivery distance vs. random assignment |

---

Developed as part of the 6th Semester Mini Project — MedRoute: A Geospatially Optimized Pharmaceutical Redistribution Network.
