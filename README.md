# Sofia.jp — All-in-One Japanese Acquisition Platform (v1)

[![JLPT N5-N1](https://img.shields.io/badge/JLPT-N5%20to%20N1-4f46e5.svg)](#)
[![Stack](https://img.shields.io/badge/Stack-FastAPI%20%2B%20React%20Vite%20%2B%20SQLite-10b981.svg)](#)
[![Status](https://img.shields.io/badge/Version-v1.0%20(Locked)-f59e0b.svg)](#)

Sofia.jp is an all-in-one Japanese language learning platform uniting structured textbook discipline with high-engagement native anime immersion, honest voice practice, reading comprehension, and spaced repetition.

---

## 🌟 The 5 Core Pillars

1. **Kanji (漢字):** Radical decomposition, stroke order animations (KanjiVG), Onyomi/Kunyomi, and Jukugo compounds.
2. **Vocabulary (語彙):** JLPT N5–N1 decks, pitch accent step contours, and **Anime Video Looping Flashcards**.
3. **Grammar (文法):** Minna no Nihongo sequence (Lessons 1–50) with 6-Phase Nuance Engine and Anime Proofs.
4. **Listening (聴解):** Millisecond-synced bilingual subtitles, blind audio ear training, and sentence looping.
5. **Reading (読解):** Tiered reading room (fairy tales, news, literature) with 4-mode Furigana toggles and comprehension quizzes.
6. **Voice Production (音声):** Character dialogue shadowing with Web Speech API transcript matching and side-by-side native audio comparison.

---

## 📁 Repository Structure

```
D:\sofia.jp\
├── docs/                      # Foundational reverse-engineering research archives
│   ├── RESOURCE.md            # JLPT Benkyo analysis
│   ├── MINNA_NIHONGO_RESOURCE.md # Minna no Nihongo analysis
│   ├── JAPANESETEST4YOU_RESOURCE.md # JapaneseTest4You analysis
│   ├── JAPANESEJLPT_RESOURCE.md # JapaneseJLPT 6-phase analysis
│   ├── AIRLEARN_VOICE_RESOURCE.md # Airlearn APK decompilation
│   ├── ANIMEI_LEARNING_RESOURCE.md # Animei.space inspection
│   └── sources.txt            # Reference links
├── backend/                   # FastAPI Python backend service
├── frontend/                  # React 18 + Vite + TypeScript PWA
├── storage/                   # Local SQLite databases and media cache
├── tools/                     # Isolated developer and reverse-engineering tools
├── MASTER_PROJECT_RESOURCES.md # Consolidated master architectural blueprint
├── PROJECT_SPEC.md            # Master product engineering specification
├── MASTER_PROMPT.md           # Developer & AI master system prompt
├── AGENT.md                   # Agent operating manual
├── BUILD_GUIDE.md             # End-to-end setup & run guide
└── IMPLEMENTATION_PLAN.md     # Sprint-by-sprint execution roadmap
```

---

## 🚀 Quickstart

See [`BUILD_GUIDE.md`](./BUILD_GUIDE.md) for full setup instructions:

```powershell
# 1. Backend (FastAPI)
cd backend
python -m venv venv
.\venv\Scripts\Activate.ps1
pip install -r requirements.txt
uvicorn app.main:app --reload

# 2. Frontend (React + Vite)
cd frontend
npm install
npm run dev
```

---

## 📄 License
Private project. Developed for personal learning use.
