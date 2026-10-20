# RESOURCE.md — JLPT Benkyo Website Analysis & Shared Learning Library Proposal

> **Research Date:** 2026-10-20  
> **Target Website:** https://jlptbenkyo.com/  
> **Inspection Method:** REA (Reverse Engineer Anything) v6.7.0 browser scenario captures using Microsoft Edge (Chromium 155.0.4283.62), headless mode, plus sitemap analysis via curl/Node.js.  
> **Purpose:** Document the target website's structure, vocabulary/kanji/grammar presentation, and design patterns to inform the development of an original JLPT Japanese-learning application.

---

## Table of Contents

1. [Project Purpose and Research Scope](#1-project-purpose-and-research-scope)
2. [Target Website and Pages Inspected](#2-target-website-and-pages-inspected)
3. [Website Navigation and Information Architecture](#3-website-navigation-and-information-architecture)
4. [Vocabulary Organization and Presentation](#4-vocabulary-organization-and-presentation)
5. [Kanji Organization and Presentation](#5-kanji-organization-and-presentation)
6. [Grammar-Related Observations](#6-grammar-related-observations)
7. [UI Components and User Flows](#7-ui-components-and-user-flows)
8. [Search, Filters, and Interactive Features](#8-search-filters-and-interactive-features)
9. [Desktop/Mobile Observations](#9-desktopmobile-observations)
10. [Screenshots and Evidence References](#10-screenshots-and-evidence-references)
11. [Observed Features vs. Inferred Features](#11-observed-features-vs-inferred-features)
12. [Shared Vocabulary and Kanji Library Proposal](#12-shared-vocabulary-and-kanji-library-proposal)
13. [Proposed Data Entities and Relationships](#13-proposed-data-entities-and-relationships)
14. [Recommended Search and Filtering Design](#14-recommended-search-and-filtering-design)
15. [Suggested Flashcard and Spaced-Repetition Integration](#15-suggested-flashcard-and-spaced-repetition-integration)
16. [Features to Implement in Our Own Application](#16-features-to-implement-in-our-own-application)
17. [Features Requiring Further Research](#17-features-requiring-further-research)
18. [Technical Implementation Recommendations](#18-technical-implementation-recommendations)
19. [Copyright, Licensing, and Attribution Considerations](#19-copyright-licensing-and-attribution-considerations)
20. [Final Summary and Next Steps](#20-final-summary-and-next-steps)

---

## 1. Project Purpose and Research Scope

This document records findings from a structured analysis of **JLPT Benkyo** (https://jlptbenkyo.com/), a website for studying Japanese vocabulary, kanji, and grammar organized by JLPT (Japanese-Language Proficiency Test) levels N5 through N1.

The goal is to understand how a real-world JLPT study website organizes and presents its learning materials, then use those findings to design an original, reusable shared learning library for our own application. **No content, branding, or copyrighted data has been copied.** This document records structural patterns, page layouts, and feature observations only.

---

## 2. Target Website and Pages Inspected

### Technology Stack

| Property | Observed Value |
|---|---|
| Static Site Generator | **Jekyll v4.4.1** (observed in `<meta name="generator">`) |
| CDN/Hosting | **Cloudflare** (observed in HTTP headers: `Server: cloudflare`) |
| CSS Framework | **Tailwind CSS** (observed via utility classes like `max-w-7xl`, `bg-white`, `shadow-sm`, `rounded-md`) |
| Analytics | Google Analytics (G-F2C7P66MP2), Microsoft Clarity (ua4a4z1tjo) |
| Ads | Google AdSense (ca-pub-7230775884324841) |
| SEO | Jekyll SEO tag v2.9.1, structured data (JSON-LD `WebSite` schema) |
| Icon System | SVG sprite sheet at `/assets/icons/sprite.svg` |

### Pages Captured with REA

| Page | URL | REA Evidence ID |
|---|---|---|
| Homepage | `https://jlptbenkyo.com/` | `ev_c9026f10b013e44e72...` |
| Vocabulary N5 List | `https://jlptbenkyo.com/vocabulary/jlpt-n5/` | Captured |
| Vocabulary Detail (会う) | `https://jlptbenkyo.com/japanese-vocabulary/あう-au-meaning/` | Captured |
| Kanji N5 List | `https://jlptbenkyo.com/kanji/jlpt-n5/` | Captured |
| Kanji Detail (日) | `https://jlptbenkyo.com/japanese-kanji/日-kanji-meaning/` | Captured |
| Grammar N5 List | `https://jlptbenkyo.com/grammar/jlpt-n5/` | Captured |
| Grammar Detail (です) | `https://jlptbenkyo.com/japanese-grammar/です-desu-meaning/` | Captured |

### Sitemap Statistics

Total URLs in sitemap: **10,882**

| Category Path | URL Count |
|---|---|
| `/japanese-vocabulary/` (detail pages) | 6,686 |
| `/japanese-kanji/` (detail pages) | 3,100 |
| `/japanese-grammar/` (detail pages) | 839 |
| `/vocabulary/` (list/pagination pages) | 135 |
| `/kanji/` (list/pagination pages) | 65 |
| `/grammar/` (list/pagination pages) | 45 |
| `/articles/` | 5 |
| Static pages (about, contact, terms, privacy, etc.) | 7 |

---

## 3. Website Navigation and Information Architecture

### Primary Navigation (Observed)

The site uses a **sticky top navigation bar** (`<nav class="bg-white shadow-sm border-b sticky top-0 z-50">`):

| Element | Type | Target |
|---|---|---|
| JLPT Benkyo logo | Link | `/` (home) |
| JLPT N5 | Dropdown button | Shows: N5 Kanji, N5 Vocabulary, N5 Grammar |
| JLPT N4 | Dropdown button | Shows: N4 Kanji, N4 Vocabulary, N4 Grammar |
| JLPT N3 | Dropdown button | Shows: N3 Kanji, N3 Vocabulary, N3 Grammar |
| JLPT N2 | Dropdown button | Shows: N2 Kanji, N2 Vocabulary, N2 Grammar |
| JLPT N1 | Dropdown button | Shows: N1 Kanji, N1 Vocabulary, N1 Grammar |
| Articles | Link | `/articles/` |
| YouTube | Link | `/video-lessons/` |
| App | External link | Apple App Store |

**Dropdowns** use CSS `group-hover` (Tailwind) — hovering the button reveals a menu with icon-labeled links for Kanji, Vocabulary, and Grammar at that JLPT level. No JavaScript toggle was observed for desktop dropdowns.

### Information Hierarchy

```
Home
├── Vocabulary
│   ├── /vocabulary/ (landing: all levels)
│   ├── /vocabulary/jlpt-n5/ → paginated list (50 per page)
│   ├── /vocabulary/jlpt-n4/ → paginated list
│   ├── /vocabulary/jlpt-n3/ → paginated list
│   ├── /vocabulary/jlpt-n2/ → paginated list
│   └── /vocabulary/jlpt-n1/ → paginated list
│       └── /japanese-vocabulary/{word}-{romaji}-meaning/ (detail page)
├── Kanji
│   ├── /kanji/ (landing: all levels)
│   ├── /kanji/jlpt-n5/ → paginated list
│   ├── /kanji/jlpt-n4/ → paginated list
│   ├── /kanji/jlpt-n3/ → paginated list
│   ├── /kanji/jlpt-n2/ → paginated list
│   └── /kanji/jlpt-n1/ → paginated list
│       └── /japanese-kanji/{kanji}-kanji-meaning/ (detail page)
├── Grammar
│   ├── /grammar/ (landing: all levels)
│   ├── /grammar/jlpt-n5/ → paginated list
│   └── ...
│       └── /japanese-grammar/{pattern}-{romaji}-meaning/ (detail page)
├── Articles (/articles/)
├── Video Lessons (/video-lessons/)
└── Static pages (about, contact, terms, privacy)
```

### Breadcrumbs (Observed)

Every detail and list page includes breadcrumb navigation:
- Home → Vocabulary → JLPT N5 → 会う
- Home → Kanji → JLPT N5 → 日
- Home → Grammar → JLPT N5 → です

### Footer (Observed)

- **Study Resources:** Vocabulary, Kanji, Grammar links
- **Information:** Articles, About Us, Contact Us, Terms of Service, Privacy Policy
- YouTube Channel link, App Store link
- Copyright: "© 2026 JLPT Benkyo. All rights reserved."

### Sidebar (Observed)

A `<aside>` / `complementary` region appears on all content pages containing:
- "JLPT Resources" links (Grammar, Kanji, Vocabulary, YouTube, iOS App)
- "Quick Links" (N5 Grammar, N5 Kanji, N5 Vocabulary, N4 Grammar)
- "Watch & Learn" with YouTube subscribe link
- "JLPT Benkyo app" with App Store download link

---

## 4. Vocabulary Organization and Presentation

### Homepage Stats (Observed)

| Level | Word Count |
|---|---|
| N5 | 600 words |
| N4 | 600 words |
| N3 | 1,650 words |
| N2 | 1,500 words |
| N1 | 2,336 words |
| **Total** | **6,500+ words** |

### Vocabulary List Page (`/vocabulary/jlpt-n5/`)

**Layout:** Tabular format with the following columns:

| Column | Description |
|---|---|
| `#` | Sequential number within the level |
| `Word` | Japanese written form (kanji + kana) + audio play button |
| `Reading` | Kana reading + romaji |
| `Meaning` | English translation |
| (unlabeled) | "Study" link to detail page |

**Concrete example from captured data (row 1):**

| # | Word | Reading | Meaning | Action |
|---|---|---|---|---|
| 1 | 会う 🔊 | あう au | to meet | Study → `/japanese-vocabulary/あう-au-meaning/` |
| 2 | 赤い 🔊 | あかい akai | red | Study → `/japanese-vocabulary/あかい-akai-meaning/` |

**Pagination (Observed):**
- Located at bottom of table inside `<nav aria-label="Pagination">`
- Previous/Next chevron buttons (disabled when at boundary)
- **Mobile:** Shows current page ± 1 (3 pages visible)
- **Desktop:** Shows current page ± 3 (7 pages visible)
- Uses separate responsive CSS classes (`md:hidden` / `hidden md:flex`)
- Page URLs: `/vocabulary/jlpt-n5/page2/`, `/vocabulary/jlpt-n5/page3/`, etc.
- ~50 rows per page (51 `<tr>` elements including header)

**Audio (Observed):**
- Each word has a blue play button (`<button class="play-audio-btn" data-text="会う">`)
- Uses **Web Speech API** (`window.speechSynthesis`) — no pre-recorded audio files
- Prefers female Japanese voice (searches for Kyoko, Yuna, Sora, Nanami, Sayaka, Rei, Maki, Akane)
- Falls back to first available `ja-*` voice
- Button shows spinner icon during playback, disables to prevent overlap
- Script source: `/assets/js/audio.js`

### Vocabulary Detail Page (`/japanese-vocabulary/あう-au-meaning/`)

**Structure (all sections observed):**

1. **Breadcrumb:** Home → Vocabulary → JLPT N5 → 会う
2. **Main Heading:** `<h1>会う</h1>`
3. **Meta line:** あう (au) · to meet · N5 JLPT
4. **Description paragraph:** "会う (au) is a JLPT N5 Japanese word meaning 'to meet'."
5. **Example Sentences** (`<h2>`):
   - Japanese sentence (kanji)
   - Kana reading
   - Romaji
   - English translation
   - Audio play button per sentence
   - Example: 彼に会う → かれにあう → Kare ni au → "I meet him."
   - Example: 明日友達に会う → あしたともだちにあう → Ashita tomodachi ni au → "I will meet my friend tomorrow."
6. **Kanji in this word** (`<h2>`):
   - Table with columns: Kanji, On'yomi, Kun'yomi, Meaning, Study
   - Links to kanji detail pages (e.g., `/japanese-kanji/会-kanji-meaning/`)
7. **Related N5 vocabulary** (`<h2>`):
   - Links to related words (e.g., 会社 → `/japanese-vocabulary/かいしゃ-kaisha-meaning/`)
8. **Navigation links:** "Back to N5 Vocabulary", "Study Kanji", "Study Grammar"

**Fields NOT observed on vocabulary detail pages:**
- Part of speech label (not verified)
- Difficulty rating (not verified)
- Tags or categories beyond JLPT level (not verified)
- User interaction features like bookmarking or progress tracking (not verified)
- Furigana rendered inline over kanji (not verified — readings shown separately)

---

## 5. Kanji Organization and Presentation

### Homepage Stats (Observed)

| Level | Kanji Count |
|---|---|
| N5 | 80 kanji |
| N4 | 170 kanji |
| N3 | 403 kanji |
| N2 | 498 kanji |
| N1 | 1,949 kanji |
| **Total** | **3,000+ kanji** |

### Kanji List Page (`/kanji/jlpt-n5/`)

**Layout:** Table with columns:

| Column | Description |
|---|---|
| `#` | Sequential number |
| `Kanji` | The kanji character |
| `Onyomi` | On'yomi reading(s) in katakana |
| `Kunyomi` | Kun'yomi reading(s) in hiragana |
| `Meaning` | English meaning(s) |
| (unlabeled) | "Study" link to detail page |

**Concrete example (row 1):**

| # | Kanji | Onyomi | Kunyomi | Meaning |
|---|---|---|---|---|
| 1 | 日 | ニチ, ジツ | ひ, -び, -か | day, sun, Japan, counter for days |

**Pagination:** Same system as vocabulary lists.

**No audio buttons on kanji list page** (observed — unlike vocabulary list).

### Kanji Detail Page (`/japanese-kanji/日-kanji-meaning/`)

**Structure (all sections observed):**

1. **Breadcrumb:** Home → Kanji → JLPT N5 → 日
2. **Main Heading:** `<h1>日</h1>`
3. **Meta line:** "day, sun, Japan, counter for days" · JLPT N5 · Usage Frequency: VERY FREQUENT 1/2500
4. **Pronunciation** (`<h2>`):
   - Onyomi (音読み): ニチ, ジツ
   - Kunyomi (訓読み): ひ, -び, -か
5. **Stroke Order** (`<h2>`):
   - Stroke count: "Strokes: 4"
   - **Replay button** to restart animation
   - **Inline SVG animation** (`<svg id="kanji-svg">`) with:
     - Gray background strokes (stroke-width: 8, color: lightgray) showing full shape
     - Black foreground strokes (stroke-width: 3) with CSS `stroke-dasharray`/`stroke-dashoffset` animation
     - Each stroke animates sequentially using `animation-delay`
     - CSS keyframe `@keyframes stroke-animation { to { stroke-dashoffset: 0; } }`
     - SVG path data embedded directly in the page (not loaded externally)
   - **No canvas or GIF** — pure SVG + CSS animation
6. **Word Examples** (`<h2>`):
   - Table: Japanese, Reading, English
   - Audio play button for each word
   - 10 example words observed (日本, 日曜日, 毎日, 今日, 明日, 昨日, 日記, 明後日, 一日, 三日)
7. **Example Sentences** (`<h2>`):
   - 4 sentences observed, each with:
     - Japanese (kanji), kana reading, romaji, English translation
     - Audio play button
8. **Navigation:** "Back to N5 Kanji", "Study Vocabulary", "Study Grammar"

**Fields NOT observed on kanji detail pages:**
- Radical/component breakdown (not verified)
- Grade level (not verified)
- Stroke order step-by-step numbered diagram (not verified — animation only)
- Links to individual vocabulary detail pages from word examples (not verified)
- Flashcard or quiz feature (not verified)

---

## 6. Grammar-Related Observations

### Homepage Stats (Observed)

| Level | Pattern Count |
|---|---|
| N5 | 114 patterns |
| N4 | 124 patterns |
| N3 | 187 patterns |
| N2 | 196 patterns |
| N1 | 218 patterns |
| **Total** | **800+ patterns** |

### Grammar List Page (`/grammar/jlpt-n5/`)

**Layout: Card-based** (not tabular like vocabulary/kanji):

Each grammar pattern is a clickable link card containing:
- **Pattern name** in Japanese + English meaning (e.g., "です · Copula; to be (am, is, are)")
- **Usage Context:** (bold label) followed by description
- **Formation:** (bold label) followed by structural formula

**Concrete example:**
> **です** · Copula; to be (am, is, are)  
> **Usage Context:** This form is used to identify or describe nouns or adjectives.  
> **Formation:** Noun + だ/です, Na-adjective + だ/です. Used at the end of the sentence.

Link: `/japanese-grammar/です-desu-meaning/`

**Pagination:** Same pagination component as vocabulary/kanji lists.

### Grammar Detail Page (`/japanese-grammar/です-desu-meaning/`)

**Structure (all sections observed):**

1. **Breadcrumb:** Home → Grammar → JLPT N5 → です
2. **Main Heading:** `<h1>です</h1>`
3. **Meta line:** (desu) Copula; to be (am, is, are) · Usage Frequency: ESSENTIAL · N5 JLPT
4. **Definition** (`<h2>`): "Used to indicate existence or identity. Equivalent to 'am', 'is', or 'are' in English."
5. **How to Use** (`<h2>`):
   - Formation rules
   - Common Usage vs. Formal Usage comparison boxes
6. **Context, Limitations and Specificities** (`<h2>`):
   - "When to use?" explanation
   - "Limitations and specificities" (e.g., "Cannot be used after I-adjectives or verbs")
7. **Example Sentences** (`<h2>`):
   - 10 example sentences observed
   - Each with: Japanese (kanji), kana reading, romaji, English translation, audio button
8. **Navigation:** "Back to N5 Grammar", "Study Vocabulary", "Study Kanji"

---

## 7. UI Components and User Flows

### Content Hierarchy Pattern

All content follows a consistent three-level hierarchy:
1. **Landing page** (`/vocabulary/`, `/kanji/`, `/grammar/`) — level selection
2. **Paginated list** (`/vocabulary/jlpt-n5/`) — browsable table/cards
3. **Detail page** (`/japanese-vocabulary/あう-au-meaning/`) — full content

### Common UI Components (Observed)

| Component | Implementation |
|---|---|
| Navigation bar | Sticky, white background, shadow, Tailwind utility classes |
| Dropdown menus | CSS `group-hover` (no JS toggle observed for desktop) |
| Tables | Standard `<table>` with `<thead>`/`<tbody>`, alternating row styles |
| Cards | `<a>` wrapper with rounded corners, shadow, hover effects |
| Buttons | Tailwind styled (`bg-blue-600`, `rounded-lg`, `hover:bg-blue-700`) |
| Audio buttons | Circular, blue, speaker icon from SVG sprite |
| Pagination | `<nav aria-label="Pagination">` with numbered links |
| Breadcrumbs | `<nav>` with `<ol>` and chevron separators |
| Sidebar | Sticky `<aside>` with resource links |
| Badges | Color-coded level indicators (green for N5, blue, purple, etc.) |
| Stroke animation | Inline SVG with CSS `stroke-dasharray` animation |

### User Flow: Finding and Studying a Word

1. Land on homepage → see "Choose Your Study Path" → click "Study Vocabulary"
2. Or: Use nav dropdown → "JLPT N5" → "N5 Vocabulary"
3. Browse paginated table → see word, reading, meaning at a glance
4. Click "Study" → arrive at detail page with examples, audio, related kanji
5. Click kanji link in detail → navigate to kanji detail page
6. Click "Back to N5 Vocabulary" → return to list

**Steps to reach a word from homepage: 3 clicks** (Home → Level → Study link)

### User Flow: Finding and Studying a Kanji

1. Nav dropdown → "JLPT N5" → "N5 Kanji"
2. Browse table → see kanji, readings, meanings
3. Click "Study" → detail page with stroke order animation, word examples, sentences
4. Click word in examples → navigates to vocabulary (not verified if clickable)
5. Click "Back to N5 Kanji" → return to list

---

## 8. Search, Filters, and Interactive Features

### Search (NOT Observed)

- **No search input** was found on the homepage, vocabulary list, kanji list, or grammar list pages
- No `<input>` elements detected in any captured DOM
- The word "search" and "filter" do not appear in the homepage or vocabulary list HTML
- **Status: Not present** (observed absence across all inspected pages)

### Filtering (NOT Observed)

- No dropdown filters, checkboxes, or category selectors were detected
- Content is organized exclusively by JLPT level through URL structure
- **Status: Not present**

### Interactive Features (Observed)

| Feature | Status | Details |
|---|---|---|
| Audio playback | ✅ Observed | Web Speech API, no pre-recorded files |
| Stroke order animation | ✅ Observed | Inline SVG with CSS animation, replay button |
| Dropdown navigation | ✅ Observed | CSS hover-based, per JLPT level |
| Pagination | ✅ Observed | Numbered pages with responsive display |
| Mobile hamburger menu | Inferred | HTML contains `md:hidden`/`hidden md:flex` responsive patterns |
| Flashcards | ❌ Not observed | No flashcard UI on any inspected page |
| Quiz/test features | ❌ Not observed | No quiz interface on any inspected page |
| User accounts/login | ❌ Not observed | No login form or user profile elements |
| Progress tracking | ❌ Not observed | No progress indicators or checkmarks |
| Spaced repetition | ❌ Not observed | No SRS interface |
| Dark mode toggle | ❌ Not observed | Only light color scheme detected |
| App promo popup | ✅ Observed | Script `/assets/js/app-promo-popup.js` loaded |

---

## 9. Desktop/Mobile Observations

All captures were taken at 1280×720 desktop viewport (REA default). Mobile observations are **inferred from HTML/CSS analysis**:

### Responsive Patterns (Inferred from DOM)

| Pattern | Evidence |
|---|---|
| Desktop navigation | `hidden md:flex` — visible only on md+ breakpoints |
| Mobile navigation | `md:hidden` elements exist (hamburger menu likely) |
| Mobile pagination | Shows ±1 page vs. desktop ±3 pages |
| Tailwind responsive prefixes | Extensive use of `sm:`, `md:`, `lg:` prefixes throughout |
| Max content width | `max-w-7xl` container (1280px) |
| Table responsiveness | Not verified — tables may overflow on mobile |
| Touch targets | Audio buttons are 32×32px (`w-8 h-8`), adequate for touch |

---

## 10. Screenshots and Evidence References

All REA evidence captures are stored in:
```
C:\Users\mukes\.gemini\antigravity\brain\62dda308-eb70-4559-8f7d-eb55c8908890\scratch\
```

| File | Content |
|---|---|
| `home-capture.json` | Full REA evidence for homepage |
| `home_dom.html` | Homepage DOM snapshot |
| `home_a11y.txt` | Homepage accessibility tree |
| `vocab-n5-capture.json` | Vocabulary N5 list REA evidence |
| `vocab_n5_a11y.txt` | Vocabulary N5 accessibility tree |
| `vocab-detail-capture.json` | Vocabulary detail page (会う) REA evidence |
| `vocab_detail_a11y.txt` | Vocabulary detail accessibility tree |
| `kanji-n5-capture.json` | Kanji N5 list REA evidence |
| `kanji_n5_a11y.txt` | Kanji N5 accessibility tree |
| `kanji-detail-capture.json` | Kanji detail page (日) REA evidence |
| `kanji_detail_a11y.txt` | Kanji detail accessibility tree |
| `grammar-n5-capture.json` | Grammar N5 list REA evidence |
| `grammar_n5_a11y.txt` | Grammar N5 accessibility tree |
| `grammar-detail-capture.json` | Grammar detail page (です) REA evidence |
| `grammar_detail_a11y.txt` | Grammar detail accessibility tree |

---

## 11. Observed Features vs. Inferred Features

### Directly Observed

- [x] Jekyll static site with Tailwind CSS
- [x] JLPT level-based organization (N5–N1)
- [x] Three content categories: Vocabulary, Kanji, Grammar
- [x] Paginated list pages (~50 items per page)
- [x] Vocabulary table columns: #, Word, Reading, Meaning, Study link
- [x] Kanji table columns: #, Kanji, Onyomi, Kunyomi, Meaning, Study link
- [x] Grammar card format with pattern, meaning, usage context, formation
- [x] Audio via Web Speech API (SpeechSynthesisUtterance, lang: ja-JP)
- [x] SVG stroke order animation with CSS stroke-dasharray
- [x] Breadcrumb navigation on all content pages
- [x] Sticky navigation bar with JLPT level dropdowns
- [x] Sidebar with resource links and quick links
- [x] Example sentences with Japanese, kana, romaji, English, audio
- [x] Cross-links between vocabulary and kanji detail pages
- [x] Related vocabulary on vocabulary detail pages
- [x] Word examples on kanji detail pages
- [x] Usage frequency badge on kanji detail pages
- [x] Copyright notice: "© 2026 JLPT Benkyo"
- [x] iOS app promoted (Apple App Store link)
- [x] YouTube channel linked
- [x] No search or filter functionality
- [x] No user accounts, login, or progress tracking
- [x] No flashcards, quizzes, or SRS features on the website

### Inferred (Reasonable but Not Directly Verified)

- [ ] Mobile hamburger menu (responsive CSS classes suggest it)
- [ ] Table scrolling on mobile (tables may overflow)
- [ ] App promo popup behavior (script loaded but not triggered in headless capture)

### Not Verified

- [ ] Part of speech for vocabulary words
- [ ] Radical/component information for kanji
- [ ] Grade level for kanji
- [ ] Step-by-step numbered stroke order (only animation observed)
- [ ] Clickable word examples on kanji pages (links to vocabulary)
- [ ] Internal API or database structure
- [ ] Content update frequency
- [ ] Server-side rendering details

---

## 12. Shared Vocabulary and Kanji Library Proposal

### Design Philosophy

Our application should have a **single shared data layer** that serves all features. Instead of duplicating vocabulary data in a flashcard database and again in a quiz database, every feature references the same canonical entities.

```
┌─────────────────────────────────────────────┐
│           SHARED LEARNING LIBRARY           │
│                                             │
│  ┌──────────┐  ┌──────┐  ┌─────────┐      │
│  │Vocabulary│  │Kanji │  │ Grammar │      │
│  │  Entity  │◄─┤Entity├─►│ Entity  │      │
│  └────┬─────┘  └──┬───┘  └────┬────┘      │
│       │           │           │            │
│  ┌────▼───────────▼───────────▼────┐       │
│  │     Example Sentences           │       │
│  └─────────────────────────────────┘       │
└──────────────────┬──────────────────────────┘
                   │
    ┌──────────────┼──────────────────┐
    │              │                  │
┌───▼───┐   ┌─────▼─────┐   ┌───────▼───────┐
│Browser│   │ Flashcard  │   │  Quiz/Test    │
│  UI   │   │    SRS     │   │   Engine      │
└───────┘   └───────────┘   └───────────────┘
```

### Key Design Decisions

1. **Vocabulary and Kanji are first-class entities** with a many-to-many relationship
2. **Grammar patterns** reference vocabulary for their example sentences
3. **User progress** is stored per-entity, not per-feature — learning 会う in flashcards updates the same record used by quizzes
4. **Example sentences** are shared — a sentence used in a vocabulary page can appear in a quiz
5. **All content is independent of presentation** — the same data drives list views, detail views, cards, quizzes

---

## 13. Proposed Data Entities and Relationships

### Vocabulary Entity

```
vocabulary {
  id              : UUID (primary key)
  written_form    : TEXT       -- e.g., "会う"
  kana_reading    : TEXT       -- e.g., "あう"
  romaji          : TEXT       -- e.g., "au"
  meanings        : TEXT[]     -- e.g., ["to meet"]
  part_of_speech  : TEXT       -- e.g., "verb" (v1 can omit, add later)
  jlpt_level      : ENUM(N5, N4, N3, N2, N1)
  sort_order      : INTEGER   -- order within level
  audio_text      : TEXT       -- text to pass to speech synthesis
  tags            : TEXT[]     -- optional, for future grouping
  source          : TEXT       -- attribution/license info
  created_at      : TIMESTAMP
  updated_at      : TIMESTAMP
}
```

### Kanji Entity

```
kanji {
  id              : UUID (primary key)
  character       : TEXT       -- e.g., "日"
  meanings        : TEXT[]     -- e.g., ["day", "sun", "Japan", "counter for days"]
  onyomi          : TEXT[]     -- e.g., ["ニチ", "ジツ"]
  kunyomi         : TEXT[]     -- e.g., ["ひ", "-び", "-か"]
  stroke_count    : INTEGER   -- e.g., 4
  jlpt_level      : ENUM(N5, N4, N3, N2, N1)
  sort_order      : INTEGER   -- order within level
  frequency_rank  : INTEGER   -- e.g., 1 (out of 2500)
  stroke_svg_data : TEXT       -- SVG path data for stroke animation (optional, v2)
  radicals        : TEXT[]     -- optional, add later
  grade           : INTEGER   -- optional, Japanese school grade
  source          : TEXT
  created_at      : TIMESTAMP
  updated_at      : TIMESTAMP
}
```

### Grammar Entity

```
grammar_pattern {
  id              : UUID (primary key)
  pattern         : TEXT       -- e.g., "です"
  romaji          : TEXT       -- e.g., "desu"
  meaning         : TEXT       -- e.g., "Copula; to be (am, is, are)"
  definition      : TEXT       -- detailed explanation
  formation       : TEXT       -- e.g., "Noun + だ/です"
  usage_context   : TEXT       -- when to use
  limitations     : TEXT       -- restrictions and notes
  jlpt_level      : ENUM(N5, N4, N3, N2, N1)
  sort_order      : INTEGER
  frequency_label : TEXT       -- e.g., "ESSENTIAL"
  source          : TEXT
  created_at      : TIMESTAMP
  updated_at      : TIMESTAMP
}
```

### Example Sentence Entity

```
example_sentence {
  id              : UUID (primary key)
  japanese_text   : TEXT       -- e.g., "彼に会う"
  kana_reading    : TEXT       -- e.g., "かれにあう"
  romaji          : TEXT       -- e.g., "Kare ni au"
  english_text    : TEXT       -- e.g., "I meet him."
  audio_text      : TEXT       -- text for speech synthesis
  source_type     : ENUM(vocabulary, kanji, grammar)
  source_id       : UUID       -- FK to the parent entity
  sort_order      : INTEGER
}
```

### Vocabulary-Kanji Junction (Many-to-Many)

```
vocabulary_kanji {
  vocabulary_id   : UUID (FK → vocabulary)
  kanji_id        : UUID (FK → kanji)
}
```

### Kanji Word Example Entity

```
kanji_word_example {
  id              : UUID (primary key)
  kanji_id        : UUID (FK → kanji)
  japanese_word   : TEXT       -- e.g., "日本"
  kana_reading    : TEXT       -- e.g., "にほん"
  romaji          : TEXT       -- e.g., "nihon"
  english_meaning : TEXT       -- e.g., "Japan"
  vocabulary_id   : UUID       -- FK → vocabulary (optional, for cross-linking)
  sort_order      : INTEGER
}
```

### Learning Progress Entity

```
learning_progress {
  id              : UUID (primary key)
  user_id         : UUID (FK → users, for multi-user; omit for v1)
  entity_type     : ENUM(vocabulary, kanji, grammar)
  entity_id       : UUID
  confidence      : INTEGER   -- 0–5 scale (0=new, 5=mastered)
  correct_count   : INTEGER   -- total correct answers
  incorrect_count : INTEGER   -- total incorrect answers
  last_reviewed   : TIMESTAMP
  next_review     : TIMESTAMP -- for SRS scheduling
  review_interval : INTEGER   -- days until next review
  ease_factor     : FLOAT     -- SRS difficulty modifier (default 2.5)
  created_at      : TIMESTAMP
  updated_at      : TIMESTAMP
  
  UNIQUE(user_id, entity_type, entity_id)
}
```

### Entity Relationship Diagram

```
┌──────────────┐       ┌──────────────────┐       ┌──────────────┐
│  vocabulary  │──M:N──│ vocabulary_kanji  │──M:N──│    kanji     │
│              │       └──────────────────┘       │              │
│  id          │                                   │  id          │
│  written_form│                                   │  character   │
│  kana_reading│                                   │  meanings    │
│  meanings    │                                   │  onyomi      │
│  jlpt_level  │                                   │  kunyomi     │
│  ...         │                                   │  stroke_count│
└──────┬───────┘                                   │  jlpt_level  │
       │                                           │  ...         │
       │ 1:N                                       └──────┬───────┘
       │                                                  │ 1:N
       ▼                                                  ▼
┌──────────────────┐                          ┌───────────────────────┐
│ example_sentence │                          │  kanji_word_example   │
│                  │                          │                       │
│  japanese_text   │                          │  japanese_word        │
│  kana_reading    │                          │  kana_reading         │
│  english_text    │                          │  english_meaning      │
│  source_type     │                          │  vocabulary_id (opt)  │
│  source_id       │                          └───────────────────────┘
└──────────────────┘

┌──────────────────┐
│ grammar_pattern  │──1:N──► example_sentence (source_type='grammar')
│                  │
│  pattern         │
│  meaning         │
│  formation       │
│  jlpt_level      │
└──────────────────┘

┌──────────────────┐
│learning_progress │ ──references──► vocabulary | kanji | grammar
│                  │
│  entity_type     │
│  entity_id       │
│  confidence      │
│  next_review     │
│  ease_factor     │
└──────────────────┘
```

---

## 14. Recommended Search and Filtering Design

Since JLPT Benkyo has **no search or filtering** (observed), this is an area where our application can significantly improve on the reference.

### Our Recommendation: Multi-Criteria Search

```
Search input: "meet" or "あう" or "会"
  → Searches across: vocabulary.written_form, vocabulary.kana_reading,
    vocabulary.meanings, kanji.character, kanji.meanings,
    grammar_pattern.pattern, grammar_pattern.meaning
  → Returns: unified results grouped by type (Vocabulary | Kanji | Grammar)
```

### Our Recommendation: Filter Controls

| Filter | Type | Values |
|---|---|---|
| JLPT Level | Multi-select checkbox | N5, N4, N3, N2, N1 |
| Content Type | Toggle tabs | All, Vocabulary, Kanji, Grammar |
| Part of Speech | Dropdown (vocabulary) | Verb, Noun, Adjective, Adverb, Particle, etc. |
| Stroke Count | Range slider (kanji) | 1–30 |
| Sort Order | Dropdown | Default, Alphabetical (kana), Frequency, Stroke count |

### Implementation Approach (v1)

For a student project, client-side filtering is simplest:
1. Load all items for the selected JLPT level into memory (max ~2,300 words for N1)
2. Use JavaScript `Array.filter()` on the loaded set
3. Debounce search input (300ms)
4. No backend search endpoint needed for v1

For v2, consider a backend search with SQLite FTS5 or PostgreSQL `tsvector`.

---

## 15. Suggested Flashcard and Spaced-Repetition Integration

JLPT Benkyo does **not** have flashcards or SRS (observed). This is a major feature gap our app should fill.

### Our Recommendation: SM-2 Based SRS

Use the **SuperMemo SM-2 algorithm** (public domain) for spaced repetition:

```
On correct answer (quality q = 0–5):
  if q >= 3 (correct):
    if repetition == 0: interval = 1 day
    elif repetition == 1: interval = 6 days
    else: interval = previous_interval × ease_factor
    repetition += 1
  else (incorrect):
    repetition = 0
    interval = 1 day
  
  ease_factor = max(1.3, ease_factor + (0.1 - (5 - q) × (0.08 + (5 - q) × 0.02)))
  next_review = now + interval
```

### Flashcard Modes

| Mode | Front | Back |
|---|---|---|
| Vocabulary: JP → EN | 会う (あう) | to meet |
| Vocabulary: EN → JP | to meet | 会う (あう) |
| Kanji: Reading | 日 | ニチ / ひ — day, sun |
| Kanji: Meaning | "day, sun, Japan" | 日 |
| Grammar: Fill-in | これは＿＿です。(This is a pen.) | ペン |

### How SRS Connects to the Shared Library

- The `learning_progress` table tracks SRS state per entity
- Flashcard review pulls entities where `next_review <= now`
- After review, update `confidence`, `correct_count`/`incorrect_count`, `next_review`, `ease_factor`
- The same progress record is visible in the vocabulary browser ("Last reviewed: 2 days ago, Next review: tomorrow")
- Quiz results also update the same `learning_progress` record

---

## 16. Features to Implement in Our Own Application

### Phase 1 (MVP)

| Feature | Priority | Notes |
|---|---|---|
| Vocabulary browser by JLPT level | High | Table view with pagination |
| Kanji browser by JLPT level | High | Table view with pagination |
| Grammar browser by JLPT level | High | Card view with pagination |
| Vocabulary detail page | High | Word, reading, meaning, examples, linked kanji |
| Kanji detail page | High | Character, readings, meanings, stroke count, word examples |
| Grammar detail page | High | Pattern, meaning, formation, examples |
| Audio playback (Web Speech API) | High | Same approach as target site |
| Responsive design | High | Mobile-first with Tailwind |
| Breadcrumb navigation | Medium | Consistent orientation |
| Cross-linking (vocab ↔ kanji) | Medium | Clickable relationships |

### Phase 2

| Feature | Priority | Notes |
|---|---|---|
| Search (client-side) | High | Across all content types |
| Filters (level, type, part of speech) | High | Dynamic filtering |
| Flashcards with SRS | High | SM-2 algorithm |
| Learning progress tracking | High | Per-entity confidence/review dates |
| SVG stroke order animation | Medium | For kanji detail pages |
| Dark mode | Medium | CSS custom properties toggle |

### Phase 3

| Feature | Priority | Notes |
|---|---|---|
| Practice quizzes | Medium | Multiple choice, fill-in-the-blank |
| Mock JLPT tests | Medium | Timed, level-appropriate |
| User accounts | Medium | Persist progress across devices |
| Reading comprehension | Low | Paragraph-level Japanese text |
| Listening practice | Low | Extended audio exercises |
| Server-side search | Low | For larger datasets |

---

## 17. Features Requiring Further Research

| Feature | What We Need | Why |
|---|---|---|
| Vocabulary part of speech | Data source for POS labels | Target site doesn't visibly show POS |
| Kanji radicals/components | Structured radical data | Target site doesn't show radicals |
| Stroke order SVG data | Open-source kanji SVG paths | Cannot copy target site's SVG data |
| Pre-recorded audio | Quality TTS alternative | Web Speech API quality varies by browser/OS |
| JLPT word/kanji lists | Authoritative official lists | Need a legitimate public-domain or CC-licensed source |
| Mobile layout testing | Actual mobile viewport captures | Only inferred from CSS in this analysis |

### Recommended Open Data Sources to Investigate

| Source | Content | License |
|---|---|---|
| [KanjiVG](https://kanjivg.tagaini.net/) | SVG stroke order data for kanji | CC BY-SA 3.0 |
| [KANJIDIC2](http://www.edrdg.org/wiki/index.php/KANJIDIC_Project) | Kanji readings, meanings, stroke counts | CC BY-SA 4.0 |
| [JMdict/EDICT](http://www.edrdg.org/wiki/index.php/JMdict-EDICT_Dictionary_Project) | Japanese vocabulary with meanings and POS | CC BY-SA 4.0 |
| [Tatoeba](https://tatoeba.org/) | Example sentences with translations | CC BY 2.0 FR |

---

## 18. Technical Implementation Recommendations

### Recommended Tech Stack (Student Project)

| Layer | Recommendation | Rationale |
|---|---|---|
| Frontend | **Next.js** or **Astro** | SSG for content pages (like Jekyll), React for interactive features |
| Styling | **Tailwind CSS** | Proven by target site, excellent for responsive design |
| Database (v1) | **JSON files** or **SQLite** | Simple, no server needed, easy to seed |
| Database (v2) | **PostgreSQL** or **Supabase** | Scalable, supports user accounts |
| Audio | **Web Speech API** | Free, no audio files to host |
| Audio (v2) | **Google Cloud TTS** or prerecorded | Consistent quality across browsers |
| Deployment | **Vercel** or **Netlify** | Free tier, automatic deployments |
| SVG Strokes | **KanjiVG** integration | CC-licensed, well-established project |

### Data Seeding Strategy

1. Source vocabulary lists from JMdict (CC BY-SA 4.0)
2. Source kanji data from KANJIDIC2 (CC BY-SA 4.0)
3. Source stroke order SVGs from KanjiVG (CC BY-SA 3.0)
4. Source example sentences from Tatoeba (CC BY 2.0)
5. Write original grammar explanations (our own content)
6. Tag everything by JLPT level using published level lists

### Storage Design for v1 (Simple)

```
/data/
  vocabulary/
    n5.json    -- Array of vocabulary objects
    n4.json
    n3.json
    n2.json
    n1.json
  kanji/
    n5.json    -- Array of kanji objects
    n4.json
    ...
  grammar/
    n5.json    -- Array of grammar pattern objects
    ...
  sentences/
    vocabulary_sentences.json  -- keyed by vocabulary ID
    kanji_sentences.json       -- keyed by kanji ID
    grammar_sentences.json     -- keyed by grammar ID
```

For v1, load JSON at build time (SSG) or on page load. Progress can be stored in `localStorage`:

```javascript
// localStorage schema
{
  "progress": {
    "vocab_<id>": { confidence: 3, correct: 12, incorrect: 2, lastReview: "2026-10-20", nextReview: "2026-10-25", easeFactor: 2.5 },
    "kanji_<id>": { ... },
    "grammar_<id>": { ... }
  }
}
```

### What to Add Later

| Component | When to Add | Why Wait |
|---|---|---|
| PostgreSQL database | When adding user accounts | localStorage is fine for single-user |
| Server-side search | When dataset exceeds ~5,000 items per page | Client-side filter works for list pages |
| Authentication | When progress sync is needed | Adds complexity |
| Pre-recorded audio | When Web Speech API quality is insufficient | Requires storage and licensing |
| Radical decomposition | When kanji study features mature | Complex data, not MVP |

---

## 19. Copyright, Licensing, and Attribution Considerations

### Target Website

- **Copyright holder:** "© 2026 JLPT Benkyo. All rights reserved."
- **Terms of Service:** Available at `/terms-of-service/`
- **Privacy Policy:** Available at `/privacy-policy/`
- This analysis documents structural patterns and publicly visible page organization only
- **No content, text, images, SVG data, or code has been copied** from the target site
- All proposed data entities use independently sourced content

### Our Application

- Use **CC BY-SA licensed data sources** (JMdict, KANJIDIC2, KanjiVG, Tatoeba) for learning content
- **Attribute all sources** per their license requirements
- Write **original grammar explanations** — do not copy from any existing site
- Design **original UI** — take inspiration from patterns but create our own visual identity
- The SVG stroke order data from the target site must not be extracted; use KanjiVG instead

### Required Attributions for Proposed Data Sources

```
This application uses data from:
- JMdict/EDICT — © Electronic Dictionary Research and Development Group (CC BY-SA 4.0)
- KANJIDIC2 — © Electronic Dictionary Research and Development Group (CC BY-SA 4.0)
- KanjiVG — © Ulrich Apel (CC BY-SA 3.0)
- Tatoeba — © Tatoeba contributors (CC BY 2.0 FR)
```

---

## 20. Final Summary and Next Steps

### What Was Inspected

Using REA v6.7.0 with headless Edge browser, we captured and analyzed **7 pages** across all 3 content types (vocabulary, kanji, grammar) on JLPT Benkyo, plus the sitemap (10,882 URLs) and the audio playback script.

### Key Findings

1. **Content is static** (Jekyll SSG) — no SPA, no client-side routing, no API
2. **Organization is clean** — three content types × five JLPT levels, three-level page hierarchy
3. **Audio uses Web Speech API** — zero hosting cost, variable quality across browsers
4. **Stroke order uses inline SVG animation** — CSS `stroke-dasharray` technique, no canvas/GIF
5. **No search, no filtering, no user accounts, no SRS, no quizzes** — major gaps our app should fill
6. **Content scale:** 6,500+ vocabulary, 3,000+ kanji, 800+ grammar patterns
7. **UI is Tailwind-based** — utility classes throughout, responsive design patterns

### Immediate Next Steps

1. **Set up the project** — Initialize Next.js/Astro with Tailwind CSS
2. **Seed data** — Download JMdict, KANJIDIC2, and KanjiVG; write a parser to generate JSON files tagged by JLPT level
3. **Build the shared library** — Implement the data entities from Section 13
4. **Build vocabulary browser** — List page + detail page as MVP
5. **Build kanji browser** — List page + detail page with stroke order
6. **Add audio** — Implement Web Speech API playback
7. **Add search** — Client-side search across all entities
8. **Add SRS flashcards** — Implement SM-2 with localStorage

### File Location

This document is saved at:

```
D:\sofia.jp\RESOURCE.md
```
