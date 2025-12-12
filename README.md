# ResaleHub — Backend API

The backend service for **ResaleHub**, a multi-marketplace inventory management system. Built with **FastAPI** and **PostgreSQL**, it handles inventory synchronization, image processing, and integrations with platforms like eBay and Poshmark.

## 🛠 Tech Stack

- **Framework:** FastAPI (Python 3.10+)
- **Database:** PostgreSQL (Production) / SQLite (Local Dev fallback)
- **ORM:** SQLAlchemy
- **Automation:** Playwright (for Poshmark automation/scraping)
- **Deployment:** Render.com

---

## 📂 Project Structure

    .
    ├── app/
    │   ├── core/           # Config, security, and constants
    │   ├── routers/        # API endpoints (Inventory, Auth, Marketplaces)
    │   ├── services/       # Business logic (eBay API, Image processing)
    │   ├── models.py       # SQLAlchemy database models
    │   ├── schemas.py      # Pydantic data schemas
    │   ├── database.py     # Database connection logic
    │   └── main.py         # App entry point
    ├── media/              # Local storage for user uploads (gitignored content)
    ├── requirements.txt    # Python dependencies
    └── README.md           # Project documentation

---

## 🚀 Local Development Setup

### 1. Prerequisites
- Python 3.10 or higher
- PostgreSQL (Optional for local dev, can use SQLite)

### 2. Create Virtual Environment

    # macOS/Linux
    python -m venv .venv
    source .venv/bin/activate

    # Windows
    python -m venv .venv
    .venv\Scripts\activate

### 3. Install Dependencies
    pip install --upgrade pip
    pip install -r requirements.txt

### 4. Configure Environment
Create a `.env` file in the root directory.
*Note: If no `.env` is provided, the app defaults to using a local SQLite database (`sql_app.db`).*

### 5. Run the Server
Start the server with hot-reload enabled:

    uvicorn app.main:app --reload --host 127.0.0.1 --port 8000

- **API Docs:** `http://127.0.0.1:8000/docs`
- **ReDoc:** `http://127.0.0.1:8000/redoc`

---

## ☁️ Deployment (Render.com)

This repository is optimized for deployment on Render.

### 1. Database Setup (PostgreSQL)
1. Create a **New PostgreSQL** database on Render.
2. Copy the **Internal Connection String**.

### 2. Service Configuration
Create a **New Web Service** connected to this repo.

**Environment Variables:**

| Variable | Value | Description |
| :--- | :--- | :--- |
| `DATABASE_URL` | `postgres://...` | Paste your Render Postgres internal URL here. |
| `SECRET_KEY` | `your-secret-key` | Random string for security/hashing. |
| `PLAYWRIGHT_BROWSERS_PATH` | `0` | Forces Playwright to use installed system browsers. |

**Build & Start Commands:**
Because this project uses **Playwright**, we must install the browsers during the build step.

* **Build Command:**
  
      pip install -r requirements.txt && python -m playwright install chromium

* **Start Command:**
  
      uvicorn app.main:app --host 0.0.0.0 --port $PORT

---

## ⚠️ Known Issues & Maintenance

### 1. Database Persistence
- **Production:** Uses PostgreSQL. The `database.py` file automatically fixes the Render connection string (replacing `postgres://` with `postgresql://`).
- **Tables:** Currently, tables are auto-created in `main.py` via `Base.metadata.create_all()`.
- **Recommendation:** Switch to **Alembic** for managing schema migrations in the future.

### 2. Circular Imports
There is a known circular import between `app/services/ebay_client.py` and `app/routers/marketplaces.py` regarding `EBAY_SCOPES`.
* **Fix:** Move shared constants to `app/core/constants.py`.

### 3. Async/Sync Blocking
Some async endpoints currently call synchronous SQLAlchemy methods (`db.query`), which may block the event loop under heavy load.
* **Fix:** Future refactoring should implement `run_in_threadpool` or switch to `asyncpg`.

---

## 📄 License
Private Repository. All rights reserved.
