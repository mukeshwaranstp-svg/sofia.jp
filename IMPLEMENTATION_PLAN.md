# IMPLEMENTATION_PLAN.md — Project Sofia.jp Phase-by-Phase Roadmap

This document outlines the granular, sprint-by-sprint engineering plan to build **Sofia.jp (v1)** following the 3-Wave strategy established in [`PROJECT_SPEC.md`](file:///D:/sofia.jp/PROJECT_SPEC.md).

---

## Master Timeline & Wave Overview

```
 ┌──────────────────────────────────────────────────────────────┐
 │                    PROJECT SOFIA.JP ROADMAP                  │
 ├──────────┬───────────────────────────┬───────────────────────┤
 │ Wave     │ Focus Area                │ Target Timeline       │
 ├──────────┼───────────────────────────┼───────────────────────┤
 │ Wave 1   │ Core Loop: Player + SRS   │ Sprints 1–4 (Weeks 1–2)│
 │ Wave 2   │ Active Skills & Practice  │ Sprints 5–8 (Weeks 3–5)│
 │ Wave 3   │ Intelligence & Automation │ Sprint 9+   (Week 6+) │
 └──────────┴───────────────────────────┴───────────────────────┘
```

---

# WAVE 1: Core Acquisition Loop (Weeks 1–2)

> **Goal:** Deliver a working anime video player with tokenized bilingual subtitles, click-to-save vocabulary/sentences, and looping video flashcards.

### Sprint 1: Architecture & Backend Proxy Gateway
- [ ] **Task 1.1: Workspace Normalization**
  - Create `backend/`, `frontend/`, `storage/`, `docs/`, `tools/`.
  - Isolate `rea/` into `tools/rea/`.
- [ ] **Task 1.2: FastAPI Application Setup**
  - Initialize FastAPI app with CORS middleware and async HTTP client (`httpx`).
  - Create database engine connecting to `storage/sofia.db`.
- [ ] **Task 1.3: Animei API Proxy Service**
  - Implement `app/services/animei_client.py` proxying:
    - `catalog/popular`
    - `catalog/search`
    - `catalog/anime/{id}`
    - `catalog/stream`
    - `subtitles/episode`
    - `words/clip`
- [ ] **Deliverable:** Backend running at `http://localhost:8000` with passing health check and streaming proxy.

---

### Sprint 2: Video Player & Subtitle Synchronization
- [ ] **Task 2.1: Frontend Vite & Tailwind Scaffolding**
  - Scaffold React 18 + Vite + TypeScript. Configure Tailwind dark theme.
  - Setup Zustand store (`useAnimeStore`).
- [ ] **Task 2.2: Anime Catalog Page**
  - Grid of anime cards with poster previews, search input, and JLPT level filters.
- [ ] **Task 2.3: HLS Video Player Integration**
  - Implement `<VideoPlayer>` with `hls.js` supporting 360p, 480p, and 720p stream selection.
- [ ] **Task 2.4: Synchronized Subtitle Panel**
  - Render transcript lines with `<TokenSpan>` elements.
  - Implement binary search $O(\log N)$ sync loop highlighting current segment on video timeupdate.
  - Add Replay Segment and Loop Segment controls.
- [ ] **Deliverable:** Playable anime episode with interactive, clickable Japanese/English subtitles.

---

### Sprint 3: Word & Sentence Saving with Clip Caching
- [ ] **Task 3.1: Save Item Modal**
  - Slide-up modal pre-filled with Kanji, Kana reading, Romaji, English gloss, and sentence context.
  - Provide both **[Save Word]** and **[Save Full Sentence]** buttons.
  - Allow adding personal notes and tags (`#slang`, `#N5`).
- [ ] **Task 3.2: IndexedDB Media Cache**
  - Initialize Dexie database (`sofia-media`) with object stores: `clips`, `audios`, and `explains`.
  - Fetch 3–5 second MP4 micro-clip via Animei `words/clip` and cache blob in IndexedDB.
- [ ] **Task 3.3: Persistence Endpoints**
  - Implement `POST /api/saved-items` and `GET /api/saved-items` storing items in SQLite.
- [ ] **Deliverable:** Clicking a word/sentence saves it to your personal deck with a cached video clip.

---

### Sprint 4: SM-2 Looping Video Flashcard Review
- [ ] **Task 4.1: SRS Engine Implementation**
  - Implement SM-2 algorithm in `app/services/srs_engine.py` with interval multiplier $2.2\times$.
  - Endpoints: `GET /api/srs/due` and `POST /api/srs/review`.
- [ ] **Task 4.2: Flashcard Review UI**
  - **Front:** Kanji or audio prompt + "Show Answer" button.
  - **Back:** Looping anime video clip from IndexedDB + furigana + translation + token breakdown.
  - Rating buttons: `Again` (1 min), `Hard` (1 day), `Good` ($2.2\times$), `Easy` (120 days).
- [ ] **Task 4.3: LocalStorage Outbox Queue**
  - Queue review events locally; flush automatically on `window.online` event.
- [ ] **Deliverable:** Fully functional, offline-capable video flashcard review session.

---

# WAVE 2: Active Skills & Structured Practice (Weeks 3–5)

> **Goal:** Add voice shadowing, role-play dialogues, static grammar lessons, Kanji viewer, curated reading passages, and JLPT quizzes.

### Sprint 5: Voice Shadowing & Role-Play Mode
- [ ] **Task 5.1: Web Speech Transcript Matcher**
  - Record learner voice using browser `MediaRecorder`.
  - Transcribe via Web Speech API (`webkitSpeechRecognition`) and match against target sentence.
  - Render color-coded word feedback (Green = matched, Red = missed).
- [ ] **Task 5.2: Self-Compare Audio Playback**
  - Provide side-by-side buttons: `[Play Native]` vs. `[Play Your Voice]`.
- [ ] **Task 5.3: Role-Play Dialogue Engine**
  - Multi-character dialogue mode: system plays Character A, learner speaks Character B.
- [ ] **Deliverable:** Working voice shadowing and conversational role-play inside the anime player.

---

### Sprint 6: Structured Grammar Lessons & Kanji Viewer
- [ ] **Task 6.1: Static Grammar Lesson Pages**
  - Bundle `grammar_n5_n3.json` containing 200+ patterns following Minna no Nihongo sequence.
  - Implement `<GrammarLessonPage>` with 6-section layout (Formula, Nuance, Examples, Pitfalls, Anime Proof, Drill).
- [ ] **Task 6.2: 3-Tier Grammar Explain Fallback**
  - Tier 1: Animei `explain` API $\rightarrow$ Tier 2: IndexedDB $\rightarrow$ Tier 3: Static JSON.
- [ ] **Task 6.3: Kanji Stroke Animation Viewer**
  - Integrate KanjiVG SVG data rendered with `dmak.js`.
  - Add freeform drawing canvas for manual practice.
- [ ] **Deliverable:** Grammar syllabus browser and animated Kanji reference page.

---

### Sprint 7: Curated Reading Room & JLPT Quiz Engine
- [ ] **Task 7.1: Tiered Reading Library**
  - Bundle 50+ public domain passages (fairy tales, Aozora Bunko, Wikipedia Simple).
  - Implement 4 Furigana toggle modes: *All*, *JLPT Filtered*, *Hover*, *Off*.
- [ ] **Task 7.2: Adaptive JLPT Quiz Generator**
  - 5 question modalities: Particle cloze, Vocab meaning, Listening cloze, Word ordering, Dokkai.
  - Log incorrect answers and automatically inject failed items into the user's SRS queue.
- [ ] **Deliverable:** Interactive reading room and JLPT practice test engine.

---

### Sprint 8: Motivation, Streaks & Stats
- [ ] **Task 8.1: Streak & Daily Activity Tracker**
  - Calculate daily consecutive study streak.
  - Display daily activity log: cards reviewed, scenes watched, lines shadowed.
- [ ] **Task 8.2: Shareable Weekly Stats Card**
  - Generate a downloadable PNG summary card of weekly progress.
- [ ] **Deliverable:** Dashboard stats and motivation tracking.

---

# WAVE 3: Intelligence & Automation (Week 6+)

> **Goal:** Live news scrapers, automated syllabus rebalancing, and comprehensibility indexes.

### Sprint 9: News Scraper & Adaptive Algorithms
- [ ] **Task 9.1: NHK News Web Easy Scraper**
  - Scheduled background worker ingesting daily NHK Easy articles with SudachiPy Furigana injection.
- [ ] **Task 9.2: Anime Comprehensibility Meter**
  - Calculate % of known vocabulary for every anime episode based on user's saved words.
- [ ] **Task 9.3: Smart Dashboard Recommendations**
  - Dynamically suggest the next optimal lesson, anime scene, or review based on weak areas.
- [ ] **Task 9.4: Group Leaderboard (Optional)**
  - Simple shared leaderboard for you and your friends.
- [ ] **Deliverable:** Fully automated, self-updating learning ecosystem.

---

## Definition of Done (DoD) per Sprint
A sprint is considered **Done** only when:
1. All code is typed strictly in TypeScript (frontend) and Pydantic (backend).
2. The user experience is tested manually in Edge / Chrome.
3. No console errors or unhandled promise rejections occur.
4. Offline capability is verified by disabling network in DevTools.
5. All research docs remain completely untouched.

*Begin execution starting with Sprint 1.*
