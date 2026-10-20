# AGENT.md — Developer & Autonomous Agent Operating Manual

**Workspace:** `D:\sofia.jp`  
**Application Name:** Sofia.jp (v1)  
**System Role:** Autonomous Software Engineer & Architect  
**Operating Environment:** Windows 11, PowerShell, Node.js 18+, Python 3.11+

---

## 1. Golden Rules of Engagement

1. **Document Preservation:**
   - NEVER overwrite or corrupt any of the six foundational research files:
     - `RESOURCE.md`
     - `MINNA_NIHONGO_RESOURCE.md`
     - `JAPANESETEST4YOU_RESOURCE.md`
     - `JAPANESEJLPT_RESOURCE.md`
     - `AIRLEARN_VOICE_RESOURCE.md`
     - `ANIMEI_LEARNING_RESOURCE.md`
   - NEVER overwrite `PROJECT_SPEC.md` or `MASTER_PROMPT.md` without explicit user instruction.
2. **Execute Wave-by-Wave (Do Not Skip Ahead):**
   - Finish, test, and verify **Wave 1 (Core Loop)** completely before writing Wave 2 code.
   - Do not attempt complex adaptive algorithms (Wave 3) while foundational video playback and flashcards are incomplete.
3. **Media Proxying Standard:**
   - Do not store or download full anime video episodes to disk.
   - Use the FastAPI backend to proxy streams and subtitles from Animei's CDN endpoints.
4. **Clean Code & Strong Typing:**
   - TypeScript for all frontend code (`frontend/src/**/*.ts`, `*.tsx`).
   - Pydantic models for all FastAPI inputs and outputs (`backend/app/models/*.py`).
   - Use async/await for all database queries and external HTTP requests.

---

## 2. Workspace Directory Layout

When developing, adhere strictly to this structure:

```
D:\sofia.jp\
├── docs/                      # Research documents (safe storage)
├── backend/                   # FastAPI Python service
│   ├── app/
│   │   ├── routers/           # anime.py, vocabulary.py, grammar.py, etc.
│   │   ├── services/          # animei_client.py, tokenizer.py, srs_engine.py
│   │   ├── models/            # SQLModel schemas
│   │   ├── data/              # Bundled JSON files (JMdict, grammar)
│   │   ├── database.py        # SQLite async engine
│   │   └── main.py            # App factory & CORS config
│   ├── requirements.txt
│   └── tests/
├── frontend/                  # React 18 + Vite + TypeScript PWA
│   ├── src/
│   │   ├── pages/             # Dashboard, AnimePlayer, Review, etc.
│   │   ├── components/        # SubtitleOverlay, TokenSpan, VideoPlayer, etc.
│   │   ├── stores/            # Zustand stores (useAnimeStore, useSrsStore)
│   │   ├── services/          # api.ts, idb.ts (Dexie database)
│   │   └── types/             # Japanese linguistic & subtitle interfaces
│   ├── package.json
│   └── vite.config.ts
├── tools/                     # External tooling (e.g., rea/)
├── storage/                   # Local database (sofia.db)
├── MASTER_PROMPT.md
├── AGENT.md
├── BUILD_GUIDE.md
├── IMPLEMENTATION_PLAN.md
└── PROJECT_SPEC.md
```

---

## 3. Subsystem Architecture & Implementation Guidelines

### 3.1 Anime Player & Subtitle Sync (`frontend/src/components/player/`)
- Attach `hls.js` to the HTML5 `<video>` element with custom buffer parameters:
  - `maxBufferLength: 30` (prevents memory spikes on long episodes).
- Use `requestAnimationFrame` for subtitle synchronization.
- **Binary Search Lookup:** Given `currentTime`, search `state.cues` in $O(\log N)$ time.
- Token clicks:
  - Set `video.pause()`.
  - Open `<SaveItemModal>` pre-filled with `token.surface`, `token.reading`, `token.base`, sentence text, and English translation.

### 3.2 Spaced Repetition (SRS) Engine (`backend/app/services/srs_engine.py`)
- Calculate intervals using the modified SM-2 formula:
  - `again`: reset to 1 minute, lapse +1.
  - `hard`: 1 day interval.
  - `good`: $\min(\text{round}(\text{prev} \times 2.2), 365)$ days.
  - `easy`: skip to 120 days.
- **Offline Outbox (`frontend/src/services/outbox.ts`):**
  - Store review events in LocalStorage FIFO queue.
  - On `window.addEventListener('online')`, POST batch to `/api/srs/review`.

### 3.3 Japanese Tokenization (`backend/app/services/tokenizer.py`)
- Use SudachiPy in Mode C for dictionary form lemmas:
  ```python
  from sudachipy import tokenizer, dictionary
  tokenizer_obj = dictionary.Dictionary(dict="full").create()
  morphemes = tokenizer_obj.tokenize(text, tokenizer.Tokenizer.SplitMode.C)
  ```
- Map each content morpheme to its corresponding **JMdict ID**.

### 3.4 Voice Shadowing (`frontend/src/components/voice/`)
- Instantiate `webkitSpeechRecognition` or `SpeechRecognition` with `lang = 'ja-JP'`.
- Record raw audio using `MediaRecorder(stream, { mimeType: 'audio/webm' })`.
- Compare recognized transcript against expected sentence:
  - Split both into words/morae.
  - Render color-coded badges (Green = matched, Red = missed).
  - Store audio Blob locally; provide `<audio>` element for side-by-side comparison.

---

## 4. Common Pitfalls & Anti-Patterns to Avoid

- **CORS Errors:** Always ensure FastAPI includes CORS middleware permitting `http://localhost:5173` (Vite dev server) with credentials enabled.
- **Anime Video Transcoding on Backend:** NEVER attempt to re-encode HLS streams on the Python server using CPU ffmpeg; let `hls.js` stream directly in the client browser.
- **Broken State During Offline:** Never block the UI waiting for external network requests. All flashcard reviews, dictionary lookups, and saved items must function against IndexedDB when offline.
- **Overwritten Reviews:** Multi-device review sync must use idempotent event logging (`user_review_events`), not destructive table updates.

---

## 5. Verification Commands

When testing the application:

```powershell
# Backend verification
cd D:\sofia.jp\backend
python -m pytest tests/

# Frontend verification
cd D:\sofia.jp\frontend
npm run lint
npm run build
```

*Adhere to this operational standard at all times.*
