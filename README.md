# UniGuide AI – Intelligent University Admission Assistant Using RAG

> **College-Level Deep Learning & Natural Language Processing Project**  
> An end-to-end, full-stack, retrieval-augmented generation (RAG) web application that empowers prospective students to ask admission questions in natural language and receive strictly document-grounded answers with exact PDF page citations.

---

## 1. Project Overview & Architecture

UniGuide AI prevents LLM hallucinations by binding every generated answer to verified semantic document chunks extracted from official university admission brochures.

```
                   DOCUMENT INGESTION PIPELINE
                 -------------------------------
Official Admission PDFs 
         ↓
PyPDF Text Extraction (Page-by-Page)
         ↓
Text Cleaning & Prompt Injection Sanitization
         ↓
Recursive Boundary-Aware Chunking (~800 chars, ~150 overlap)
         ↓
Dense Vector Embedding (`sentence-transformers/all-MiniLM-L6-v2`)
         ↓
FAISS Vector Database (Inner-Product Cosine Similarity)

                   QUESTION ANSWERING PIPELINE
                 -------------------------------
Student Natural Language Query
         ↓
Query Vectorization (`all-MiniLM-L6-v2`)
         ↓
FAISS Similarity Search (Top-4 Chunks)
         ↓
Similarity Score Threshold Check (>= 0.40)
       ├── Below Threshold ──> Strict Polite Refusal
       │                       ("I couldn't find reliable information...")
       └── Above Threshold ──> Pass Context to Gemini LLM
                                     ↓
                               Grounded Answer Generation with Citations
                                     ↓
                               Display Answer with Document & Page Badges
```

---

## 2. Technology Stack

- **Frontend**:
  - React.js 19 + Vite 6
  - Tailwind CSS v4
  - Lucide React (Icons)
  - Recharts (Interactive Analytics & Seat Distributions)
- **Backend**:
  - Python 3.10+ / 3.14
  - FastAPI (REST API with OpenAPI / Swagger UI at `/docs`)
  - Uvicorn (ASGI Web Server)
- **RAG & Deep Learning**:
  - `sentence-transformers` (`all-MiniLM-L6-v2` - 384 dimensions)
  - `faiss-cpu` (Facebook AI Similarity Search - IndexFlatIP)
  - `pypdf` (Page-level PDF text extraction)
  - `reportlab` (Generation of sample admission brochures)
  - `google-genai` (Google Gemini 2.5 / 1.5 Flash)
- **Database & Security**:
  - SQLite + SQLAlchemy 2.0
  - Bcrypt password hashing
  - PyJWT authentication tokens

---

## 3. Application Modules

1. **Home / Landing Page**: Project presentation, live metrics counter, step-by-step RAG visual diagram, and quick module entry points.
2. **Student Dashboard**: Real-time statistics, seat capacity charts (Recharts), popular admission questions, and recent interaction feed.
3. **AI Admission Chatbot (Main Feature)**:
   - Natural language queries with typing animation
   - Verifiable document source citations (Document name, page number, similarity score)
   - Collapsible source snippets
   - Conversation history tracking and clear conversation button
   - Out-of-domain query refusal ("I couldn't find reliable information about this in the available university documents...")
4. **Explore Universities**: Directory of institutions with location filters, degree types, official website links, and course detail modals.
5. **Academic Courses Explorer**: Course listings with seat quotas, annual tuition fees, required subjects, and 1-click eligibility check.
6. **Eligibility Checker**: Form evaluating student marks (Math, Physics, Chemistry, Total %) against documented criteria with clear verdicts:
   - `Eligible`
   - `Not Eligible`
   - `Unable to determine eligibility` (when criteria are unlisted)
7. **Admission Document Center**: Multi-file PDF drag-and-drop uploader, page extraction counter, chunk counter, delete document, and vector index rebuild.
8. **Fee Structure & Scholarships**: Tuition fees comparative charts, hostel accommodation tiers (Single, Double, AC, Triple), scholarship eligibility criteria, and required financial aid documents.
9. **Admission Timeline**: Chronological milestone tracker for application windows, ASAT entrance exam, counselling, and verification.
10. **Admin Panel**: Role-protected dashboard for managing documents, courses, universities, and rebuilding the FAISS index.

---

## 4. Setup Instructions

### Prerequisites
- **Python**: 3.10 or newer (tested on Python 3.14)
- **Node.js**: v18 or newer (tested on v24.13)
- **Git**

---

### Step 1: Backend Setup

1. Open a terminal in the project directory:
   ```bash
   cd backend
   ```

2. Install Python dependencies:
   ```bash
   pip install -r requirements.txt
   ```

3. Configure Environment Variables:
   Copy `.env.example` to `.env`:
   ```bash
   copy .env.example .env
   ```

4. *(Optional but Recommended)* Add your Google Gemini API Key:
   - Visit [Google AI Studio](https://aistudio.google.com/app/apikey) to generate a free API key.
   - Open `backend/.env` and set:
     ```env
     GEMINI_API_KEY=AIzaSyYourActualKeyHere
     ```
   *(Note: If no API key is provided, the application will automatically fall back to local document excerpt synthesis with verifiable citations without throwing errors).*

5. Start the Backend Server:
   ```bash
   python -m uvicorn app.main:app --host 127.0.0.1 --port 8000 --reload
   ```
   The backend API and Swagger UI will be available at:
   - API Docs: `http://127.0.0.1:8000/docs`
   - Health Check: `http://127.0.0.1:8000/api/health`

---

### Step 2: Frontend Setup

1. Open a new terminal in the project directory:
   ```bash
   cd frontend
   ```

2. Install Node dependencies:
   ```bash
   npm install
   ```

3. Start the Vite development server:
   ```bash
   npm run dev
   ```

4. Open your browser and navigate to:
   ```
   http://localhost:5173/
   ```

---

## 5. Demo Credentials

The database is automatically pre-seeded with sample users and admission data upon first startup:

| Role | Email | Password |
|---|---|---|
| **Administrator** | `admin@uniguide.edu` | `Admin@123` |
| **Student** | `student@uniguide.edu` | `Student@123` |

*(You can also use the **1-Click Demo Account Switcher** directly in the top navigation bar).*

---

## 6. Pre-Indexed Sample Demonstration Documents

The system automatically generates and indexes 3 realistic demonstration PDF documents located in `sample_documents/`:

1. **`Apex_Global_University_Admissions_Guide_2025.pdf`**:
   - B.Tech eligibility criteria (10+2 with Physics & Math, 60% aggregate, 50% in Math/Physics, JEE/ASAT entrance).
   - BCA and MCA course criteria.
   - Mandatory document verification checklist.
   - Important admission timeline & dates for 2025 intake.
   - Step-by-step application procedure.

2. **`Apex_University_Fee_and_Hostel_Structure.pdf`**:
   - Course-wise annual tuition fees (CSE: $12,500, AI&DS: $13,000, BCA: $7,500, MCA: $9,000, MBA: $14,000).
   - Semester exam fees & refundable caution deposits.
   - Campus hostel tiers (Triple Non-AC: $2,200, Double Non-AC: $2,800, Double AC: $3,600, Single Suite: $4,800).
   - Fee refund and cancellation rules.

3. **`Apex_Scholarship_and_Eligibility_Criteria.pdf`**:
   - Chancellor's Gold Merit (100% waiver for 95%+ marks).
   - Dean's Excellence (50% waiver for 90-94.9%).
   - Academic Distinction (25% waiver for 85-89.9%).
   - EWS Need-based grant (up to 40% waiver).
   - Sports & Women in STEM waivers.
   - Required scholarship verification documents and renewal conditions.

---

## 7. Automated Test Suite

A comprehensive test suite verifying the 10 benchmark questions and eligibility logic is provided:

```bash
python backend/test_pipeline.py
```

### Benchmark Questions Tested:
1. `What is the eligibility criteria for B.Tech admission?` -> **Grounded** (Cited: Apex Admissions Guide, Page 1)
2. `What documents are required for admission?` -> **Grounded** (Cited: Apex Admissions Guide, Page 1)
3. `What is the fee structure for Computer Science?` -> **Grounded** (Cited: Fee Schedule, Page 1)
4. `Are scholarships available for students?` -> **Grounded** (Cited: Scholarships Guide, Page 1)
5. `What is the minimum percentage required?` -> **Grounded** (Cited: Apex Admissions Guide, Page 1)
6. `What is the admission procedure?` -> **Grounded** (Cited: Apex Admissions Guide, Page 2)
7. `What are the hostel fees?` -> **Grounded** (Cited: Fee Schedule, Page 1)
8. `What is the application deadline?` -> **Grounded** (Cited: Apex Admissions Guide, Page 2)
9. `What is the price of an iPhone in the cafeteria?` -> **Safely Refused** (Strict refusal returned)
10. `Can astronauts land their spaceship in the football field?` -> **Safely Refused** (Strict refusal returned)

---

## 8. License & Academic Attribution

This project is built for educational, deep learning, and retrieval-augmented generation demonstrations. All sample university details are fictional and designed for research validation.
