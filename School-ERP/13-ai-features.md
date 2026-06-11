# 13. AI Features & Services

## 1. Overview
The School ERP system integrates a comprehensive, modular AI ecosystem designed to provide advanced capabilities such as automated grading, document digitization, and AI-assisted content generation. This service operates as a stateless, high-performance microservice leveraging Computer Vision and Large Language Models (LLMs).

## 2. Core AI Modules

### 2.1 OMR Processing Engine (`omr`)
- **Purpose:** Automates the evaluation of scanned exam sheets.
- **Capabilities:** Aligns images, extracts student roll numbers, and grades bubble sheets.
- **Technology:** Uses OpenCV for classical computer-vision-based Optical Mark Recognition. Processing is handled asynchronously using Celery and Redis to support batch processing of hundreds of sheets.

### 2.2 AI Lesson Planner (`lesson_planner`)
- **Purpose:** Empowers teachers by auto-generating comprehensive lesson plans.
- **Capabilities:** Creates structured plans based on provided topics, grades, and subjects utilizing an LLM backbone.

### 2.3 AI Homework Generator (`homework_generator`)
- **Purpose:** Streamlines the creation of student assignments.
- **Capabilities:** Dynamically crafts targeted homework assignments and interactive quizzes using generative AI.

### 2.4 Math to LaTeX Converter (`math_latex`)
- **Purpose:** Digitizes mathematical equations from images.
- **Capabilities:** Leverages local LLM inference to accurately transcribe complex math equations into LaTeX format for digital use.

### 2.5 OCR Engine (`ocr_engine`)
- **Purpose:** Shared infrastructure for high-performance document reading (e.g., forms, textbooks).
- **Capabilities:** Employs advanced image preprocessing (deskewing, shadow removal) and strict quality gates. Uses PaddleOCR in-memory singleton for ultra-fast, multi-lingual text extraction.

## 3. Architecture & Technology Stack
The AI Service runs as an independent microservice communicating with the main ERP backend.

- **API Framework:** FastAPI + Uvicorn
- **Async Workers:** Celery + Redis (for batch jobs like OMR)
- **Computer Vision:** OpenCV, PyMuPDF, EasyOCR, PyTesseract, PaddleOCR
- **LLM/AI Integration:** Ollama (local inference)
- **Database:** PostgreSQL / SQLite (managed via SQLModel)
- **Containerization:** Docker & Docker Compose
