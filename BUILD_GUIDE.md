# BUILD_GUIDE.md — Project Sofia.jp Developer Build & Setup Guide

This guide provides end-to-end instructions for installing, configuring, running, and testing **Project Sofia.jp (v1)** on your local development machine.

---

## 1. Prerequisites & Environment Setup

Ensure the following tools are installed on your Windows machine:

| Prerequisite | Minimum Version | Verification Command |
| :--- | :--- | :--- |
| **Node.js** | v18.0.0+ | `node --version` |
| **npm** | v9.0.0+ | `npm --version` |
| **Python** | 3.11+ | `python --version` |
| **Git** | 2.30+ | `git --version` |
| **PowerShell** | 5.1+ / 7+ | `$PSVersionTable.PSVersion` |

---

## 2. Workspace Initialization

Run the following commands in PowerShell from the project root (`D:\sofia.jp`):

```powershell
# 1. Navigate to workspace
cd D:\sofia.jp

# 2. Create standardized directory structure
New-Item -ItemType Directory -Force -Path "backend\app\routers"
New-Item -ItemType Directory -Force -Path "backend\app\services"
New-Item -ItemType Directory -Force -Path "backend\app\models"
New-Item -ItemType Directory -Force -Path "backend\app\data"
New-Item -ItemType Directory -Force -Path "backend\tests"
New-Item -ItemType Directory -Force -Path "frontend"
New-Item -ItemType Directory -Force -Path "storage"
New-Item -ItemType Directory -Force -Path "docs"
New-Item -ItemType Directory -Force -Path "tools"
```

---

## 3. Backend Setup (FastAPI + Python 3.11)

### 3.1 Create Virtual Environment & Install Dependencies

```powershell
cd D:\sofia.jp\backend

# Create Python virtual environment
python -m venv venv

# Activate virtual environment
.\venv\Scripts\Activate.ps1

# Upgrade pip
python -m pip install --upgrade pip
```

### 3.2 Create `backend/requirements.txt`

```txt
fastapi>=0.110.0
uvicorn[standard]>=0.28.0
sqlmodel>=0.0.16
pydantic>=2.6.0
httpx>=0.27.0
sudachipy>=0.6.8
sudachidict_full>=20240109
edge-tts>=6.1.10
python-multipart>=0.0.9
pytest>=8.0.0
pytest-asyncio>=0.23.0
```

Install requirements:
```powershell
pip install -r requirements.txt
```

### 3.3 Configure Environment Variables (`backend/.env`)

Create `D:\sofia.jp\backend\.env`:

```env
SOFIA_ENV=development
PORT=8000
DATABASE_URL=sqlite:///../storage/sofia.db
ANIMEI_API_BASE=https://animei.space/api/v1
CORS_ORIGINS=http://localhost:5173,http://127.0.0.1:5173
```

### 3.4 Initialize Database & Core Tables

```powershell
# Run the database initialization script
python -c "from app.database import init_db; init_db()"
```

---

## 4. Frontend Setup (React 18 + Vite + TypeScript)

### 4.1 Initialize Vite Project

```powershell
cd D:\sofia.jp\frontend

# Scaffold Vite React TypeScript app (if not already scaffolded)
npm create vite@latest . -- --template react-ts

# Install core production dependencies
npm install hls.js dexie zustand lucide-react clsx tailwind-merge

# Install developer dependencies & Tailwind CSS
npm install -D tailwindcss postcss autoprefixer @types/node
npx tailwindcss init -p
```

### 4.2 Configure Tailwind CSS (`frontend/tailwind.config.ts`)

```typescript
import type { Config } from 'tailwindcss';

export default {
  content: ['./index.html', './src/**/*.{js,ts,jsx,tsx}'],
  darkMode: 'class',
  theme: {
    extend: {
      colors: {
        sofia: {
          dark: '#0f172a',
          card: '#1e293b',
          accent: '#4f46e5',
          gold: '#f59e0b',
          success: '#10b981',
          danger: '#ef4444',
        },
      },
    },
  },
  plugins: [],
} satisfies Config;
```

---

## 5. Running the Application Concurrently

For optimal local development, run the backend and frontend in two separate PowerShell terminals:

### Terminal 1: Backend API Gateway
```powershell
cd D:\sofia.jp\backend
.\venv\Scripts\Activate.ps1
uvicorn app.main:app --reload --host 127.0.0.1 --port 8000
```
*API Docs available at: `http://127.0.0.1:8000/docs`*

### Terminal 2: Frontend Vite Development Server
```powershell
cd D:\sofia.jp\frontend
npm run dev
```
*Web App available at: `http://localhost:5173`*

---

## 6. Verification Checklist

To confirm that your build is working correctly, test these key operations:

1. **Backend Health Check:**
   ```powershell
   curl.exe http://127.0.0.1:8000/api/health
   # Expected response: {"status": "ok", "app": "Sofia.jp"}
   ```
2. **Animei Catalog Proxy:**
   ```powershell
   curl.exe http://127.0.0.1:8000/api/anime/popular
   # Expected response: JSON array of popular anime with Shikimori poster links
   ```
3. **Database Creation:**
   Verify that `D:\sofia.jp\storage\sofia.db` has been created and contains tables:
   `dictionary_words`, `grammar_points`, `user_saved_items`, `user_srs`.
4. **Frontend Playback:**
   Open `http://localhost:5173` in Microsoft Edge or Google Chrome, select an episode (e.g., *Naruto* Ep 1), and verify that:
   - Video streams smoothly via HLS.
   - Japanese & English subtitles appear in the transcript panel.
   - Clicking a word highlights the morpheme and opens the save modal.

*End of BUILD_GUIDE.md*
