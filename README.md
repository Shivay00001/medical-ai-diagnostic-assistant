# Medical AI Diagnostic Assistant

An enterprise-grade solution engineered for high performance.

![Language](https://img.shields.io/badge/Language-Python-blue)
![Framework](https://img.shields.io/badge/Framework-FastAPI-009688)
![Model](https://img.shields.io/badge/Model-v2.4.1-orange)
![Status](https://img.shields.io/badge/Status-Active-success)
![License](https://img.shields.io/badge/License-VisionQuantech%20Custom-red)

## 🚀 Overview

Welcome to the **Medical AI Diagnostic Assistant** repository. This project is built to deliver a robust and scalable solution tailored to modern development standards. It exposes a lightweight, containerized **FastAPI** inference service designed to serve diagnostic predictions over HTTP, with a model versioning scheme (`v2.4.1`) reported via the health endpoint.

The service is designed as the inference backbone for a medical diagnostic pipeline — accepting structured patient/feature data and returning a predicted diagnostic class along with a confidence score.

## ✨ Features

- **High Performance:** Optimized for speed and efficiency, powered by FastAPI's async-capable ASGI stack (Uvicorn).
- **Scalable Architecture:** Designed to grow with your needs — stateless HTTP API, horizontally scalable behind any load balancer.
- **Clean Codebase:** Follows best practices and industry standards.
- **Secure by Default:** Engineered with security in mind — `.gitignore` rigorously excludes secrets, credentials, API keys, and environment files.
- **Containerized Deployment:** Ships with a production-style `Dockerfile` for one-command deployment on any laptop or server.
- **ML/AI Ready Stack:** Dependencies include `numpy`, `pandas`, `scikit-learn`, and `torch` for real model integration.

## 🏗️ Architecture / How It Works

The system is a single-service REST API built on **FastAPI + Uvicorn**, packaged in a slim Python 3.9 Docker image.

```
┌─────────────┐      HTTP POST /predict       ┌──────────────────────────────┐
│   Client    │ ────────────────────────────▶ │  FastAPI App (main.py)       │
│ (Web/CLI/   │                               │  ┌────────────────────────┐  │
│  Frontend)  │ ◀──────────────────────────── │  │ Inference Engine       │  │
└─────────────┘   {class_id, confidence}      │  │ (NumPy vector pipeline)│  │
                                              │  └────────────────────────┘  │
                                              │  Port 8000 (Uvicorn)         │
                                              └──────────────────────────────┘
```

### API Endpoints

| Method | Endpoint   | Description |
|--------|------------|-------------|
| `GET`  | `/`        | Health check — returns `{"status": "operational", "model_version": "v2.4.1"}` |
| `POST` | `/predict` | Accepts a JSON payload (`dict`) and returns a diagnostic prediction: `{"class_id": int, "confidence": float}` |

### Inference Flow

1. A client `POST`s a JSON payload to `/predict`.
2. The inference engine generates a **128-dimensional feature vector** (`np.random.rand(128)`).
3. The predicted class is derived via `argmax` over the vector; the maximum value is returned as the confidence score.
4. Interactive API documentation is automatically available at `/docs` (Swagger UI) and `/redoc`.

> ⚠️ **Note:** The current inference logic is a **simulated placeholder** — see the [Workability Assessment](#-workability-assessment) section below for details.

## 🛠️ Prerequisites

Ensure you have the following installed in your environment before proceeding:
- **Python 3.9+** (for local development) — or **Docker** (for containerized runs, no local Python needed)
- Standard development tools (`git`, `pip`)

## 📦 Installation

Follow standard installation steps for `Python` to set up the project locally:

1. Clone the repository:
   ```bash
   git clone https://github.com/Shivay00001/medical-ai-diagnostic-assistant.git
   ```
2. Navigate to the project directory:
   ```bash
   cd medical-ai-diagnostic-assistant
   ```
3. Install dependencies according to the standard `Python` ecosystem:
   ```bash
   pip install -r requirements.txt
   ```

## 💻 Usage

### Running Locally (Python)

Start the Uvicorn server:

```bash
uvicorn main:app --host 0.0.0.0 --port 8000
```

Then verify the service:

```bash
# Health check
curl http://localhost:8000/

# Request a prediction
curl -X POST http://localhost:8000/predict \
  -H "Content-Type: application/json" \
  -d '{"patient_features": [0.5, 1.2, 3.4]}'
```

Expected responses:

```json
// GET /
{"status": "operational", "model_version": "v2.4.1"}

// POST /predict
{"class_id": 42, "confidence": 0.987}
```

Ensure all environment variables and configurations are set prior to execution (none are strictly required for the current build).

## 🐳 Running with Docker (Recommended)

The repository includes a ready-to-use `Dockerfile` based on `python:3.9-slim`. This is the easiest way to run the service on any laptop or server.

### Option 1: Docker Build & Run

```bash
# Build the image
docker build -t medical-ai-diagnostic-assistant .

# Run the container (maps container port 8000 to local port 8000)
docker run -d -p 8000:8000 --name medical-ai medical-ai-diagnostic-assistant
```

### Option 2: Docker Compose

A `docker-compose.yml` is not included by default, but you can create one instantly:

```yaml
version: "3.9"
services:
  api:
    build: .
    ports:
      - "8000:8000"
    restart: unless-stopped
```

Then run:

```bash
docker-compose up --build
```

### Verify the Deployment

```bash
curl http://localhost:8000/
# → {"status":"operational","model_version":"v2.4.1"}
```

The API docs will be available at **http://localhost:8000/docs**.

## 🔬 Workability Assessment

In the interest of full transparency, here is an honest evaluation of the repository's current state:

**What works today:**
- ✅ The FastAPI application **runs correctly** — both endpoints (`/` and `/predict`) are functional and return valid JSON.
- ✅ The Dockerfile is valid and will successfully build and serve the app via Uvicorn.
- ✅ Dependency management (`requirements.txt`) and secret hygiene (`.gitignore`) are solid.

**What is NOT production-ready:**
- ❌ **The "AI" is simulated.** The `/predict` endpoint generates a random 128-dimensional vector and returns `argmax` of random noise. It does **not** load or run any trained medical model, despite `torch` and `scikit-learn` being listed as dependencies. The `model_version: "v2.4.1"` label refers to a model artifact that is not present in the repository.
- ❌ **No input validation.** The `/predict` endpoint accepts an untyped `dict` with no schema (e.g., no Pydantic model), no feature-shape checks, and no error handling.
- ❌ **No tests, no model weights, no training pipeline, no data preprocessing code** are included.
- ❌ **Medical safety:** Nothing in this codebase should be used for real diagnostic decisions. There is no validation, calibration, regulatory compliance, or clinical safeguards.

**Verdict:** This repository is a **well-structured scaffolding / API skeleton** — a solid foundation for a medical AI inference service — but it is **not a functional diagnostic system**. To reach production readiness, it requires: (1) a real trained model artifact wired into `/predict`, (2) Pydantic request/response schemas, (3) a test suite, and (4) appropriate clinical/regulatory review before any medical use.

## 🤝 Contributing

Contributions, issues, and feature requests are welcome! Feel free to check the issues page. High-impact contributions include wiring in a real model, adding Pydantic schemas, and building a test suite.

## 📝 License

This project is licensed under the **VisionQuantech Custom Commercial License** (see `LICENSE`):

- **Non-financial / educational use:** Free.
- **Personal revenue-generating use:** Requires a 15–30% revenue share.
- **Business / enterprise use:** Requires a separate commercial license — contact **visionquantech@proton.me**.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND.