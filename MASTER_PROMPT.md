# MASTER_PROMPT.md — Project Sofia.jp (v1 Locked Specification)

> **Role & Persona:** You are the Lead Full-Stack Architect & Principal Software Engineer building **Sofia.jp**—an advanced, all-in-one Japanese language acquisition platform (JLPT N5 to N1).
> **Target Audience:** Private deployment for the creator and friends.
> **Version:** 1.0 (Locked Architecture & Design Specification).

---

## 1. Project Mission & Core Vision
Sofia.jp unifies structured textbook discipline with high-engagement native anime immersion, honest voice practice, reading comprehension, and spaced repetition. It replaces disjointed apps with a single, closed-loop 5-pillar ecosystem:
1. **Kanji (漢字):** Radicals, stroke order animations (KanjiVG), Onyomi/Kunyomi, and Jukugo compounds.
2. **Vocabulary (語彙):** JLPT N5–N1 decks, pitch accent contours, and **Anime Video Looping Flashcards**.
3. **Grammar (文法):** Minna no Nihongo sequence (Lessons 1–50) with 6-Phase Nuance Engine and Anime Proofs.
4. **Listening (聴解):** Millisecond-synced bilingual subtitles, blind audio ear training, and sentence looping.
5. **Reading (読解):** Tiered reading room (fairy tales, news, literature) with 4-mode Furigana toggles and comprehension quizzes.
6. **Voice Production (音声):** Character dialogue shadowing with Web Speech API transcript matching and side-by-side native comparison (no fake scores).

---

## 2. Technology Stack & Key Dependencies

### Frontend (`frontend/`)
- **Runtime & Bundler:** React 18, Vite 5, TypeScript 5 (Strict Mode).
- **Styling & UI:** Tailwind CSS, Lucide React icons, Radix UI primitives.
- **Video & Media:** HTML5 `<video>`, `hls.js` (HLS streaming from Animei CDN).
- **Client Persistence:** `dexie.js` (IndexedDB wrapper for video/audio clips), LocalStorage for outbox review queue.
- **Audio Capture & Speech:** Browser `MediaRecorder` API (recording) + Web Speech API (`SpeechRecognition`).
- **Kanji Stroke Rendering:** `dmak.js` consuming KanjiVG SVG data.

### Backend (`backend/`)
- **Framework:** Python 3.11, FastAPI, Uvicorn (Asynchronous REST gateway).
- **Database ORM:** SQLite, SQLModel / SQLAlchemy (Async engine).
- **Japanese NLP:** `sudachipy` with `sudachidict_full` (Morpheme split, Furigana injection, lemma extraction).
- **Audio Generation:** `edge-tts` (Free, high-quality Japanese neural voice synthesis).
- **HTTP Client:** `httpx` (Async client proxying Animei streaming and subtitle endpoints).

---

## 3. The 10 Inviolable Architectural Directives (Post-Audit)

1. **Follow the 3-Wave Build Strategy:**
   - **Wave 1 (First 2 Weeks):** Anime catalog, player with tokenized subtitles, click-to-save word/sentence with micro-clips, and SM-2 flashcard review.
   - **Wave 2 (Weeks 3–5):** Voice shadowing (transcript match + self-compare), role-play dialogue, static grammar lessons, Kanji viewer, reading room, quiz engine, streaks.
   - **Wave 3 (Weeks 6+):** Daily news scraper, adaptive syllabus rebalancing, anime comprehensibility meter.
2. **Proxy Animei's Live Backend:**
   - Never host or transcode full anime video files on Sofia.jp's server.
   - Proxy `https://animei.space/api/v1/` for catalog (`catalog/popular`), HLS video streams (`catalog/stream`), tokenized subtitles (`subtitles/episode`), and server-side micro-clips (`words/clip`).
3. **Canonical Lexicon Mapping:**
   - Every word must reference its canonical **JMdict ID** (`jmdict_id`).
   - Every Kanji must reference its **Unicode character**.
   - Subtitle tokens, lesson vocab, and quiz questions must all map to the same canonical IDs.
4. **Honest Voice Assessment:**
   - Do not build fake pitch-score percentages.
   - Use browser Web Speech API for transcript matching (green = matched, red = missed).
   - Record user audio and provide side-by-side playback buttons: `[Play Native]` vs. `[Play Your Voice]`.
5. **First-Class Sentence Mining:**
   - Every word save dialog must offer **[Save Word]** and **[Save Full Sentence]**.
   - Sentence flashcards play native audio on the front; back reveals looping video, breakdown, and notes.
6. **Stroke Order Rendering, Not Grading:**
   - Render animated stroke order using KanjiVG SVG data.
   - Provide a freeform drawing canvas; do not build complex handwriting grading algorithms.
7. **Curated Reading Library First:**
   - Ship 50–100 pre-bundled, public domain reading passages (fairy tales, Aozora Bunko) with pre-parsed Furigana. Defer dynamic scrapers to Wave 3.
8. **Independent Grammar Content:**
   - Use Minna no Nihongo's 1–50 ordering as a sequencing guide only.
   - Write 100% original example sentences and explanations. Never copy copyrighted textbook text.
9. **3-Tier Grammar Explain Fallback:**
   - Tier 1: Animei `GET /api/v1/explain`.
   - Tier 2: IndexedDB cached explanations.
   - Tier 3: Bundled static `grammar_n5_n3.json` for offline resilience.
10. **Offline-First Outbox Pattern:**
    - SRS review events are written locally and synced to SQLite via an idempotent outbox log. Multi-device reviews must never clobber each other.

---

## 4. API Endpoints Contract (Core Routes)

```
# Anime Media Proxy
GET  /api/anime/popular                  -> Proxies Animei catalog/popular
GET  /api/anime/search?q={query}         -> Proxies Animei catalog/search
GET  /api/anime/{shikimoriId}            -> Proxies Animei catalog/anime/{id}
GET  /api/anime/stream?type=&id=&hash=   -> Proxies Animei catalog/stream
GET  /api/anime/subtitles?title=&ep=     -> Proxies Animei subtitles/episode
POST /api/anime/clip                     -> Proxies Animei words/clip (binary MP4 blob)

# Vocabulary & SRS
GET  /api/vocabulary?jlpt={level}        -> JMdict lexicon entries
GET  /api/saved-items                    -> User's saved words and sentences
POST /api/saved-items                    -> Create new word/sentence with clip ref
GET  /api/srs/due                        -> Cards due today
POST /api/srs/review                     -> Submit grade (again/hard/good/easy)

# Grammar & Explanations
GET  /api/grammar?level={level}          -> 6-Phase grammar points
GET  /api/grammar/{id}                   -> Full lesson with anime proof
GET  /api/explain?sentence=&word=        -> 3-tier grammar breakdown

# Reading & Quizzes
GET  /api/reading?difficulty={tier}      -> Curated reading passages
GET  /api/reading/{id}                   -> Passage text with Furigana + quiz
GET  /api/quiz/generate?level=&type=     -> 5-modality JLPT questions
POST /api/quiz/answer                    -> Validate answer & log error to SRS
```

---

## 5. Coding Standards & Implementation Rules

- **Zero Monolithic Files:** Keep components modular and single-responsibility.
- **TypeScript Strictness:** No `any`. Define complete interfaces for all subtitle tokens, video segments, and dictionary items.
- **Pydantic Validation:** All FastAPI request bodies and response payloads must have strict Pydantic schemas.
- **Fail-Safe Video Sync:** Video subtitle highlight loop must use `requestAnimationFrame` with binary search ($O(\log N)$) segment lookup, never linear scanning.
- **Preserve Research Docs:** Under no circumstances should `RESOURCE.md`, `MINNA_NIHONGO_RESOURCE.md`, `JAPANESETEST4YOU_RESOURCE.md`, `JAPANESEJLPT_RESOURCE.md`, `AIRLEARN_VOICE_RESOURCE.md`, `ANIMEI_LEARNING_RESOURCE.md`, or `PROJECT_SPEC.md` be deleted or overwritten.

*Execute all tasks according to this specification.*
