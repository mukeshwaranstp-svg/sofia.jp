# RESOURCE.md — Minna Nihongo Website Analysis & Level-Based Learning Feed Architecture

> **Inspection Date:** 2026-10-20  
> **Target Website:** https://www.minnanihongo.com/  
> **Analysis Engine:** REA (Reverse Engineer Anything) v6.7.0 browser scenario runner with Microsoft Edge (Chromium 155.0.4283.62), HTTP header inspection, and sitemap topology mapping.  
> **Mission:** Document the target website's news publication, learning updates, courses, and interactive exercise systems to design an original, level-based Japanese learning feed and practice exercise engine for our own platform.

---

## Table of Contents

1. [Research Objective and Inspection Date](#1-research-objective-and-inspection-date)
2. [Target Website and Pages Inspected](#2-target-website-and-pages-inspected)
3. [Website Navigation and Information Architecture](#3-website-navigation-and-information-architecture)
4. [News and Learning-Update Organization](#4-news-and-learning-update-organization)
5. [Types of Educational Content Found](#5-types-of-educational-content-found)
6. [Exercise and Practice Activity Analysis](#6-exercise-and-practice-activity-analysis)
7. [Observed Question Formats and Feedback Mechanisms](#7-observed-question-formats-and-feedback-mechanisms)
8. [Content Categories, Levels, and Filtering](#8-content-categories-levels-and-filtering)
9. [UI Components and User Flows](#9-ui-components-and-user-flows)
10. [Desktop/Mobile Observations](#10-desktopmobile-observations)
11. [Evidence, URLs, and Screenshot References](#11-evidence-urls-and-screenshot-references)
12. [Observed, Inferred, and Unverified Features](#12-observed-inferred-and-unverified-features)
13. [Proposed Level-Based Learning Feed](#13-proposed-level-based-learning-feed)
14. [Proposed Feed-Item Data Structure](#14-proposed-feed-item-data-structure)
15. [Original Exercise System and Sample Questions](#15-original-exercise-system-and-sample-questions)
16. [Shared Vocabulary, Kanji, and Grammar Integration](#16-shared-vocabulary-kanji-and-grammar-integration)
17. [Learner Progress and Mistake Tracking](#17-learner-progress-and-mistake-tracking)
18. [Suggested Database Structure](#18-suggested-database-structure)
19. [Features to Implement in Our Own Application](#19-features-to-implement-in-our-own-application)
20. [Further Research Required](#20-further-research-required)
21. [Copyright, Licensing, and Attribution Considerations](#21-copyright-licensing-and-attribution-considerations)
22. [Implementation Recommendations and Next Steps](#22-implementation-recommendations-and-next-steps)

---

## 1. Research Objective and Inspection Date

* **Objective:** Conduct an architectural, educational, and user-experience investigation into **Minna no Nihongo** (`https://www.minnanihongo.com/`) to analyze how it structures daily news, updates, educational courses, quizzes, and mock tests. The verified findings serve as the foundation for developing our own Japanese-learning platform featuring an original level-based learning feed and interactive exercises.
* **Inspection Date:** 2026-10-20
* **Research Boundary:** Non-invasive inspection of public client interfaces, DOM structures, and network transactions. **No copyrighted text, worksheets, questions, or audio files are copied.** All schemas and exercises proposed are independent clean-room designs.

---

## 2. Target Website and Pages Inspected

### Technical Infrastructure (Observed)

| Parameter | Observed Technical Details |
|---|---|
| **Hosting & Delivery** | **Vercel** (`Server: Vercel`, `X-Vercel-Id: bom1::...`), Edge Network with HSTS enabled |
| **Frontend Framework** | **Single Page Application (SPA)** built with Vite/Rollup (`/assets/index-9598bb5a.js`, `/assets/vendor-ui-176c3c59.css`, `/assets/index-f01cc7b7.css`) |
| **UI Component Library** | **Ant Design v5** (identified via `ant-btn`, `ant-radio`, `ant-wave-target`, and `rc-` component tokens in DOM) with custom utility styling |
| **Telemetry & Metrics** | Vercel Speed Insights (`/_vercel/speed-insights/script.js`), Google Analytics / GTM, Google AdSense (`ca-pub-3037939172957715`) |
| **Monetization & Auth** | Freemium model: Ad-supported free tier with a "Turn off ads" paid upgrade (`/pricing`), user registration (`/register`), and login (`/login`) for score persistence |

### Inspected Pages & REA Captures

| Page Role | URL | Inspected Attributes |
|---|---|---|
| **Platform Homepage** | `https://www.minnanihongo.com/` | Hero section, course directory links, tools, placement test entry |
| **News Feed Hub** | `https://www.minnanihongo.com/news` | Daily feed list, date stamps, proficiency tags, search bar, topic groupings |
| **Daily News Article** | `https://www.minnanihongo.com/news/easy-japanese-news-20261020` | Multi-story layout, furigana annotations, embedded video listening, translations, source links |
| **News Courses Hub** | `https://www.minnanihongo.com/news-courses` | Categorized news courses (Economy, Sports, Society, Disaster) with drills |
| **Courses Catalog** | `https://www.minnanihongo.com/courses` | 40 courses filtered by JLPT level (N5–N1), categories, previews, lesson breakdowns |
| **Course Detail** | `https://www.minnanihongo.com/courses/n5-topic-based-conversation-weather-seasons-small-talk` | Lesson outline, vocab/kanji inventories, JSON-LD Schema graph |
| **Lesson & Drill Page** | `.../lessons/n5-tws-01-todays-weather` | Dialogue presentation, vocab lists, in-lesson exercises (6 questions), related quiz links |
| **Quizzes Hub** | `https://www.minnanihongo.com/quizzes` | Asynchronous quiz directory, title search, level/category dropdowns |
| **Interactive Quiz** | `https://www.minnanihongo.com/quiz/jlpt-n5-listening-exercise-38` | Interactive audio questions, radio selection, answer summary chips, live submission, scoring & leaderboard |
| **Mock Tests Hub** | `https://www.minnanihongo.com/jlpt-mock-tests/n5` | Full-length timed mock exams (67 questions, 90 mins), breakdown vs. short quizzes |
| **Placement Test** | `https://www.minnanihongo.com/placement-test` | 30 questions (~18 mins) evaluating Vocabulary, Grammar, Kanji, Listening, and Reading |
| **Speaking Exchange** | `https://www.minnanihongo.com/speaking` | Peer-to-peer Jitsi matching queue grouped by JLPT band |
| **Study Blog / Posts** | `https://www.minnanihongo.com/posts` | 82 educational guides, textbook comparisons, self-study roadmaps |

### Sitemap Breakdown (3,716 Total URLs)

- **Kanji Characters:** 1,979 detail pages (`/kanji/{character}`)
- **Courses:** 1,502 URLs (`/courses/...` including modules and lessons)
- **News Updates:** 88 daily news editions (`/news/easy-japanese-news-YYYYMMDD`)
- **Study Blog Posts:** 82 articles (`/posts/...`)
- **JLPT Mock Tests:** 31 URLs (`/jlpt-mock-tests/...`)
- **News Topic Courses:** 10 URLs (`/news-courses/...`)
- **NAT-TEST Mock Tests:** 7 URLs (`/nat-mock-tests/...`)
- **Static & Tool Pages:** ~17 URLs (about, pricing, placement-test, hiragana-chart, etc.)

---

## 3. Website Navigation and Information Architecture

### Top Navigation Bar (Observed)

The sticky top navigation bar presents clear entry points into every primary learning activity:

```
[ みんな日本語 Logo ]
├── News            → /news (Daily Easy Japanese News feed)
├── Courses ▾       → /courses (Drop-down to JLPT N5–N1 levels and News-courses)
├── Quizzes         → /quizzes (Single-skill drills)
├── Speaking        → /speaking (Peer matching via Jitsi)
├── Mock tests ▾    → /mock-tests (JLPT & NAT-TEST timed exams)
├── Tools ▾         → /tools (Hiragana/Katakana PDF charts, Kanji lookup, Dictionary, Placement test)
├── Posts           → /posts (Study guides & blog articles)
├── Free Plan       → /pricing (Upgrade/remove ads)
├── Language Switch → English / Vietnamese / Global
└── User Profile    → Login / Register / Progress dashboard
```

### Hierarchy & Cross-Linking

The architecture connects theoretical learning with practical testing:

```
                          ┌─────────────┐
                          │  Home Page  │
                          └──────┬──────┘
                                 │
     ┌──────────────┬────────────┼────────────┬─────────────┐
     ▼              ▼            ▼            ▼             ▼
┌─────────┐   ┌───────────┐ ┌─────────┐ ┌───────────┐ ┌───────────┐
│  News   │   │  Courses  │ │ Quizzes │ │Mock Tests │ │   Tools   │
│  Feed   │   │  Catalog  │ │ Library │ │  Engine   │ │ & Charts  │
└────┬────┘   └─────┬─────┘ └────┬────┘ └─────┬─────┘ └───────────┘
     │              │            │            │
     ▼              ▼            ▼            ▼
┌─────────┐   ┌───────────┐ ┌─────────┐ ┌───────────┐
│Daily Art│──►│Structured │─►Single-  │─►Full-length│
│(5 stories)  │Lessons    │ │Skill    │ │Timed Exam │
└─────────┘   └───────────┘ └─────────┘ └───────────┘
```

1. **News to Courses:** Daily news articles link to `/news-courses` where articles are assembled into topical courses.
2. **Courses to Quizzes:** Every course lesson links directly to related standalone quizzes (e.g., Lesson 1 links to 12 listening quizzes).
3. **Quizzes to Mock Tests:** Quizzes prompt learners who master short drills to validate stamina on full timed mock tests.

---

## 4. News and Learning-Update Organization

### Daily News Feed Structure (`/news`)

The news feed is maintained as a **daily recurring publication**:

* **URL Pattern:** `/news/easy-japanese-news-YYYYMMDD` (e.g., `/news/easy-japanese-news-20261020` published on the day of inspection).
* **Proficiency Level Tagging:** Every edition displays an explicit JLPT range: `Japanese Listening (N3-N5)`.
* **Publication Presentation:** Each entry shows:
  - Title badge: `Daily Japanese`
  - Formatted date: `20 October 2026`
  - Headline with emoji icon: `Japan's MMX Spacecraft Successfully Launches Toward Mars 🇯🇵`
  - View counter: `4 views`
* **Search & Filter:** Includes an inline search box (`searchbox "Search news…"`) and a direct CTA button leading to topical groupings (`/news-courses`).

### Anatomy of a Daily News Edition (Observed)

Each daily edition is structured as a multi-story digest containing **5 curated news items**:

1. **Multimedia Integration:** Embedded YouTube iframe providing the listening audio/video clip corresponding to the stories.
2. **Simplified Japanese Article (`やさしい日本語`):**
   - Headline in kanji with full kana furigana annotations in parentheses.
   - Simplified grammar structures adapted to N4/N3 levels.
   - Spacing between Japanese grammatical segments (`日本語 / にほんご / の / 火星 / かせい...`) to aid parsing.
3. **Parallel English Translation:** A clean English translation placed directly below each story paragraph.
4. **Authoritative Source Provenance:** Each story links back to the original source article on **NHK News Web Easy** (`https://news.web.nhk/newsweb/...`).

---

## 5. Types of Educational Content Found

The platform offers six distinct categories of educational resources:

| Content Type | Format | Target Skill | Pedagogical Function |
|---|---|---|---|
| **Daily News Digest** | Multimedia article (Video + Text + Furigana + Translation) | Listening, Reading Comprehension, Current Vocabulary | Contextual learning using real-world Japanese events |
| **News-Based Courses** | Themed lesson tracks (Economy, Sports, Society, Disaster) | Topic Vocabulary, Cloze, Sentence Matching | Transitioning news reading into systematic vocabulary acquisition |
| **Structured Text Courses** | Multi-lesson textbooks (Conversation, Marugoto, Speed Master) | Speaking, Grammar Rules, Practical Dialogues | Systematic level progression (N5 to N1) |
| **Single-Skill Quizzes** | 5 to 10 question interactive tests | Listening, Particle Usage, Kanji Readings, Grammar | Rapid targeted knowledge verification with instant feedback |
| **Full Mock Tests** | 67 questions, 90-minute timed exams | Time Management, Exam Stamina, Comprehensive Scoring | Exact simulation of official JLPT and NAT-TEST exam conditions |
| **Reference Guides** | Long-form blog articles (82 posts) | Study Roadmaps, Scoring Analysis, Textbook Reviews | Self-study guidance and learning strategy |

---

## 6. Exercise and Practice Activity Analysis

### Drill Types Observed

Direct inspection of lessons, quizzes, and course descriptions verified three distinct practice formats:

1. **Audio-Based Listening Comprehension (Interactive Web Quiz):**
   - **Learning Objective:** Test auditory comprehension of spoken dialogues under exam conditions.
   - **Mechanism:** HTML5 `<audio>` player paired with 4-choice radio questions.
   - **Interaction:** Learner plays audio, selects choice, tracks answered status in progress sidebar, submits, and reviews correct answers with a downloadable PDF transcript.
2. **In-Lesson Practice Quizzes (Course Modules):**
   - **Learning Objective:** Reinforce vocabulary and grammar points introduced in the preceding dialogue.
   - **Scope:** 6 to 10 questions per lesson.
   - **Format:** Fill-in-the-blank particle selection (e.g., `東京の天気（ ）どうですか`), verb conjugation matching (`降ります → 降って`), and vocabulary definitions.
3. **Topical News Drills (News Courses):**
   - **Learning Objective:** Retention of topical terminology.
   - **Format:** Cloze exercises, term matching, and reading comprehension.

---

## 7. Observed Question Formats and Feedback Mechanisms

### Interactive Quiz Mechanism (Verified via REA Form Submission)

During inspection of `/quiz/jlpt-n5-listening-exercise-38`, an automated form submission scenario was executed to examine evaluation and feedback behavior:

```
[ Question Q1 ] ──► [ Audio Player ] ──► [ Radiogroup: 1 | 2 | 3 | 4 ]
                                                       │
[ Progress Sidebar ] ◄── Updates state on click ───────┘
  (0/5 → 5/5, Progress bar fills, Question chips highlight)
       │
[ Submit Button ] (Validates: minimum 50% completed required to unlock)
       │
       ▼ Click Submit
[ Results Screen Rendered ]
  ├── Final Score: 0/100 (Correct: 0/5, Progressbar: 0%)
  ├── Transcript Resource: Link to downloadable PDF transcript
  ├── Social Leaderboard: Top 10 learners with scores and crown badges
  ├── Detailed Question Breakdown:
  │     ├── Marked Choice: "1 (your choice)" [checked, disabled]
  │     ├── Status Indicator: "Incorrect" [Red badge]
  │     └── Answer Key: "Correct answer: 3" [Green highlight]
  ├── Retake Action: "Reset" button clears selections for fresh attempt
  └── Conversion Modal: Dialog prompting guest to Register/Login to save score
```

---

## 8. Content Categories, Levels, and Filtering

### Filtering Capabilities (Observed)

* **Courses:** Filterable by **JLPT Level** (Tabs: N5 [17], N4 [10], N3 [7], N2 [3], N1 [3]), **Category** (Conversation, Grammar, Kanji, Listening, Reading, Vocabulary), **Sort Order** (Newest, Popular), and a full-text search box (`textbox "Search by title or description"`).
* **Quizzes:** Filterable by **Level** (combobox: Any, N5, N4, N3, N2, N1), **Category**, and **Title Search**.
* **Mock Tests:** Dedicated sub-hubs for each level (`/jlpt-mock-tests/n5` through `/n1`), cleanly isolating content by user proficiency.

---

## 9. UI Components and User Flows

### Component Inventory (Observed)

| Component | Technical Implementation |
|---|---|
| **Sticky Header** | Top banner with logo, navigation links, dropdown indicators, and language selector |
| **Interactive Radio Groups** | Ant Design `ant-radio-wrapper` with wave animations on selection |
| **Progress Trackers** | Dynamic progress bar (`role="progressbar"`) tracking answered percentage |
| **Question Status Chips** | Numbered buttons (`Q1` to `Q5`) acting as quick-jump anchors |
| **Leaderboard Table** | Ranked list with gold/silver/bronze crown icons and user handles |
| **Conversion Modals** | Ant Design dialogs prompting guest learners to register to preserve streaks and test histories |
| **Furigana Text** | Standard HTML `<ruby>` tags with `<rt>` readings or inline parentheses annotations |

### User Study Flow: From News to Practice (Observed)

1. **Discovery:** User opens `/news`, reviews latest headlines with JLPT proficiency tags (`N3-N5`).
2. **Study:** User reads simplified news story, watches listening clip, checks English translation.
3. **Practice:** User navigates to `/news-courses` or clicks an in-lesson quiz link.
4. **Execution:** User answers 5 questions, checks progress on sidebar chips, clicks "Submit".
5. **Review:** User views score (e.g. 80/100), reads question-level answer keys, downloads transcript PDF, and sees ranking.

---

## 10. Desktop/Mobile Observations

* **Desktop Layout:** Tested at 1280×720 viewport. Multi-column layouts with dedicated sidebars for quiz navigation, sticky course headers, and broad table views for scores and courses.
* **Mobile Adaptations (Inferred from CSS & DOM):** Ant Design responsive grid (`ant-col-xs`, `ant-col-md`), drawer navigation for mobile menus, stacked card views replacing horizontal tables on narrow screens, and touch-friendly radio targets (40px minimum touch target).

---

## 11. Evidence, URLs, and Screenshot References

All raw REA evidence records, captured DOM trees, and accessibility hierarchies are archived in the workspace scratch directory:

* `minna-home-capture.json` / `minna_home_dom.html` / `minna_home_a11y.txt`: Homepage snapshot.
* `minna-news-capture.json` / `minna_news_dom.html` / `minna_news_a11y.txt`: News feed catalog and search interface.
* `minna-article-capture.json` / `minna_article_dom.html` / `minna_article_a11y.txt`: 20 Oct 2026 daily news edition (5 stories, video, translations).
* `minna-courses-capture.json` / `minna_courses_dom.html` / `minna_courses_a11y.txt`: Course directory with level filter tabs.
* `minna-quiz-detail-capture.json` / `minna_quiz_detail_a11y.txt`: Pre-submission listening quiz state.
* `minna-quiz-submit-capture.json` / `submit_a11y.txt`: Post-submission scoring, leaderboard, and question review state.
* `minna-placement-capture.json` / `minna_placement_a11y.txt`: Placement test interface.

---

## 12. Observed, Inferred, and Unverified Features

### Directly Observed
- [x] Vercel-hosted Single Page Application using Ant Design UI components.
- [x] Daily news feed with real-world articles (NHK News Web Easy source provenance).
- [x] Multi-story news layout with furigana, English translations, and embedded video.
- [x] Multiple choice quizzes with audio player integration.
- [x] Real-time question progress tracker and quick-jump question buttons.
- [x] Strict submission validation (minimum 50% answered before submit unlocks).
- [x] Immediate scoring (0–100), answer key display, and downloadable PDF transcripts.
- [x] Public score leaderboard showing top 10 users.
- [x] Guest conversion dialog prompting registration to save progress.
- [x] Dedicated JLPT N5–N1 mock tests (67 questions, 90 minutes).
- [x] Placement test (30 questions, 18 minutes, 5 skill categories).
- [x] Peer speaking queue connecting learners via Jitsi rooms.

### Inferred (High Probability)
- [ ] User authentication uses JWT stored in cookies/localStorage (inferred from `/api/auth/` and `/api/me` endpoints in `robots.txt`).
- [ ] Streak and presence tracking for gamification (inferred from `/api/streak` and `/api/presence` in `robots.txt`).
- [ ] Backend database utilizes relational models (courses, lessons, quizzes, questions, submissions) backed by PostgreSQL/MySQL.

### Not Verified
- [ ] Server-side grading logic for complex open-ended translation tasks (only radio-choice quizzes observed).
- [ ] Real-time speech recognition for the speaking feature (uses external Jitsi rooms for peer audio, not automated speech evaluation).
- [ ] Direct offline mobile app availability (promotes responsive web app).

---

## 13. Proposed Level-Based Learning Feed

Based on the verified strengths of Minna no Nihongo's daily updates, we propose an original **Level-Based Personalized Learning Feed** for our application.

### Feed Concept & Personalization Logic

1. **Default Level Alignment:** When a learner sets their target level to **N4**, the feed prioritizes N4 activities, while interleaving N5 reinforcement (20%) and N3 stretch goals (10%).
2. **Daily Rhythm:**
   - **Morning:** Daily News Bite (simplified text + vocabulary list).
   - **Midday:** 3-Minute Grammar Drill (fill-in-the-blank).
   - **Evening:** Listening Dialogue or Reading Challenge.
3. **Adaptive Mistake Injection:** Any question missed during a quiz or mock test automatically reappears in the feed 24 hours later as a "Daily Revision Card".

```
┌────────────────────────────────────────────────────────┐
│                   LEARNER FEED (N4)                    │
├────────────────────────────────────────────────────────┤
│ [ Filter Pills: All | 📰 News | 📝 Quiz | 🎧 Audio ]   │
│                                                        │
│ ┌────────────────────────────────────────────────────┐ │
│ │ ⚡ DUE FOR REVIEW · Mistake Recovery               │ │
│ │ Particle Drill: 雨が降って（　）ました。           │ │
│ │ [ Start 2-Minute Review → ]                        │ │
│ └────────────────────────────────────────────────────┘ │
│                                                        │
│ ┌────────────────────────────────────────────────────┐ │
│ │ 📰 DAILY NEWS BITE · N4 · 4 min read               │ │
│ │ Japan Unveils High-Speed Maglev Train Extension    │ │
│ │ 🎧 Audio included · 8 target words · 1 quiz        │ │
│ │ [ Read & Listen → ]                                │ │
│ └────────────────────────────────────────────────────┘ │
│                                                        │
│ ┌────────────────────────────────────────────────────┐ │
│ │ 🎯 DAILY CHALLENGE · N4 Grammar                    │ │
│ │ Mastering 「〜てみる」: Trying something out       │ │
│ │ 5 quick multiple-choice questions                  │ │
│ │ [ Start Quiz → ]                                   │ │
│ └────────────────────────────────────────────────────┘ │
└────────────────────────────────────────────────────────┘
```

---

## 14. Proposed Feed-Item Data Structure

Every feed item conforms to a unified schema supporting diverse content types while linking directly to our shared library:

```typescript
export type JLPTLevel = 'N5' | 'N4' | 'N3' | 'N2' | 'N1';
export type FeedItemType = 'news' | 'vocab_daily' | 'grammar_tip' | 'reading_challenge' | 'listening_drill' | 'review_mistake';
export type SkillCategory = 'vocabulary' | 'kanji' | 'grammar' | 'reading' | 'listening';

export interface FeedItem {
  id: string;                         // UUID v4
  title: string;                      // Display title
  summary: string;                    // Short teaser (1-2 sentences)
  contentType: FeedItemType;          // Feed item categorization
  jlptLevel: JLPTLevel;               // Target proficiency
  skillCategory: SkillCategory;       // Primary skill practiced
  publishedAt: string;                // ISO 8601 publication timestamp
  estimatedMinutes: number;           // Estimated completion time
  difficultyScore: number;            // 1.0 (easy) to 5.0 (hard) within level
  
  // Shared Library References
  relatedVocabularyIds: string[];     // FKs to shared vocabulary entity
  relatedKanjiIds: string[];          // FKs to shared kanji entity
  relatedGrammarIds: string[];        // FKs to shared grammar entity
  
  // Interactive Exercise Linkage
  exerciseId?: string;                // FK to interactive exercise
  
  // Provenance & Licensing
  contentSource: {
    originType: 'original' | 'curated_news' | 'cc_licensed';
    attributionText?: string;
    sourceUrl?: string;
    license: 'All Rights Reserved' | 'CC BY-SA 4.0' | 'Public Domain';
  };
  
  bodyContent?: {
    japaneseText: string;             // Text with furigana or ruby markup
    englishTranslation: string;       // Parallel English meaning
    audioUrl?: string;                // Audio file or TTS marker
    videoEmbedUrl?: string;           // Optional listening stream
  };
}
```

---

## 15. Original Exercise System and Sample Questions

Below are 10 original exercise types designed for our platform. All questions, sentences, and explanations are original clean-room creations:

### 1. Vocabulary Meaning Selection
* **Level:** N5 | **Skill:** Vocabulary
* **Prompt:** Select the correct English meaning for **毎朝（まいあさ）**.
* **Choices:** A) Every week | B) Every morning | C) Tomorrow morning | D) Every evening
* **Expected Answer:** B) Every morning
* **Explanation:** 「毎（まい）」 means every, and 「朝（あさ）」 means morning. Together, 毎朝 means every morning.

### 2. Kanji Reading Selection
* **Level:** N5 | **Skill:** Kanji
* **Prompt:** Choose the correct reading for the underlined kanji: この**<u>本</u>**は面白いです。
* **Choices:** A) ほん | B) ぼん | C) もん | D) はん
* **Expected Answer:** A) ほん
* **Explanation:** The kanji 「本」 when referring to a book is read as 「ほん」 (hon).

### 3. Grammar Fill-in-the-Blank
* **Level:** N4 | **Skill:** Grammar
* **Prompt:** 雨が降っているので、出かけるのを（　）ました。
* **Choices:** A) やめ | B) はじめ | C) つづけ | D) なおし
* **Expected Answer:** A) やめ
* **Explanation:** The structure 「動詞の辞書形 ＋ のをやめる」 means "to stop doing [verb]". Since it is raining, the speaker decided to cancel going out.

### 4. Sentence Ordering (Scramble)
* **Level:** N4 | **Skill:** Grammar
* **Prompt:** Rearrange the words to create a natural sentence. What goes in position ★?  
  日曜日、私は（ 1: 友達 2: と 3: 図書館 4: へ ）★ 行きました。
* **Choices:** A) 友達 | B) と | C) 図書館 | D) へ
* **Expected Answer:** D) へ (Sentence order: 友達 [1] と [2] 図書館 [3] へ [4] 行きました)
* **Explanation:** The particle 「と」 marks the companion (友達と), and 「へ」 marks the destination (図書館へ).

### 5. Correct Particle Selection
* **Level:** N5 | **Skill:** Particles
* **Prompt:** 私はバス（　）学校へ行きます。
* **Choices:** A) に | B) で | C) を | D) が
* **Expected Answer:** B) で
* **Explanation:** The particle 「で」 indicates the means or method of transportation (バスで = by bus).

### 6. Reading Comprehension (Short Passage)
* **Level:** N4 | **Skill:** Reading
* **Passage:**  
  田中さんは毎朝7時に起きます。朝ごはんを食べた後で、30分散歩します。会社は9時からですが、田中さんはいつも8時半に着きます。
* **Question:** 田中さんは出勤する前に何をしますか。
* **Choices:** A) 運動して朝ごはんを食べる | B) 朝ごはんを食べて散歩する | C) 7時半まで寝る | D) 会社で朝ごはんを食べる
* **Expected Answer:** B) 朝ごはんを食べて散歩する
* **Explanation:** The passage states 「朝ごはんを食べた後で、30分散歩します」 (After eating breakfast, he walks for 30 minutes).

### 7. Short Dialogue Comprehension
* **Level:** N4 | **Skill:** Listening / Conversational Grammar
* **Dialogue:**  
  A: 「週末、どこかへ行きましたか。」  
  B: 「いいえ、雨がひどかったので、家で映画を（　）。」
* **Choices:** A) 見てしまいました | B) 見ていました | C) 見るつもりです | D) 見られました
* **Expected Answer:** B) 見ていました
* **Explanation:** B describes an ongoing past action during the rainy weekend: 家で映画を見ていました (I was watching movies at home).

### 8. Error Correction
* **Level:** N3 | **Skill:** Grammar / Nuance
* **Prompt:** Identify the unnatural sentence:
* **Choices:**  
  A) 部屋を掃除しておきました。  
  B) 窓が開けてあります。  
  C) 窓が開いています。  
  D) 窓を開けています。
* **Expected Answer:** B) is grammatically awkward if intending spontaneous state; if intentional state, it requires an agent context. (Focus drill on 「〜てある」 vs 「〜ている」).
* **Explanation:** 「開けてあります」 implies someone opened it purposefully and it remains open.

### 9. Translation & Cloze Composition
* **Level:** N4 | **Skill:** Production
* **Prompt:** Complete the translation: "Please do not take photos here."  
  ここで写真を（　）（　）ください。
* **Choices:** A) とら / ないで | B) とり / ないで | C) とって / なくて | D) とる / な
* **Expected Answer:** A) とら / ないで
* **Explanation:** The polite negative request pattern is 「動詞ない形 ＋ ないでください」. For 撮る (Group 1), the nai-form is 撮らない.

### 10. Mistake-Based Review Question
* **Level:** Adaptive (Learner's historic mistake) | **Skill:** Personalized Recovery
* **Prompt:** [Generated automatically when learner fails a particle question involving transitive direct objects].  
  コーヒー（　）飲みます。
* **Choices:** A) を | B) が | C) に | D) で
* **Expected Answer:** A) を
* **Explanation:** Direct objects of transitive verbs like 飲む take the accusative particle 「を」.

---

## 16. Shared Vocabulary, Kanji, and Grammar Integration

To prevent data silos between the **Reading Feed**, **Exercise Engine**, and **Reference Library**, all modules bind directly to centralized entities:

```
┌──────────────────────────────────────────────────────────────┐
│                    CENTRAL ENTITY REGISTRY                   │
│                                                              │
│   ┌──────────────┐    ┌──────────────┐    ┌──────────────┐   │
│   │  Vocabulary  │    │    Kanji     │    │   Grammar    │   │
│   │  (ID: v_102) │    │  (ID: k_045) │    │  (ID: g_018) │   │
│   │   「毎朝」   │    │    「朝」    │    │ 「〜をやめる」│  │
│   └──────┬───────┘    └──────┬───────┘    └──────┬───────┘   │
└──────────┼───────────────────┼───────────────────┼───────────┘
           │                   │                   │
     ┌─────┴───────────────────┴───────────────────┴─────┐
     ▼                                                   ▼
┌─────────────────────────┐                 ┌─────────────────────────┐
│     FEED ITEM ENTITY    │                 │     EXERCISE ENTITY     │
│                         │                 │                         │
│ id: "feed_oct20_news"   │                 │ id: "ex_particle_402"   │
│ relatedVocab: [v_102]   │                 │ targetVocab: [v_102]    │
│ relatedKanji: [k_045]   │                 │ targetGrammar: [g_018]  │
│ relatedGrammar: [g_018] │                 │ explanation: uses g_018 │
└─────────────────────────┘                 └─────────────────────────┘
```

### Operational Workflows

1. **Vocabulary Hover in News:** When a learner reads a news item, words matching `relatedVocabularyIds` display instant popover definitions rendered directly from the shared vocabulary entity.
2. **Integrated Kanji Extraction:** Tapping a kanji in any exercise dialogue opens a drawer showing stroke order, on/kun readings, and radical breakdown directly from the central Kanji repository.
3. **Cross-Feature Mastery:** Passing an exercise targeting `v_102` (`毎朝`) automatically marks that vocabulary item as "Encountered & Practiced" in the learner's vocabulary dictionary and flashcard review queue.

---

## 17. Learner Progress and Mistake Tracking

### SuperMemo SM-2 Spaced Repetition Integration

To ensure durable memory retention, every exercise attempt is evaluated using the **SM-2 algorithm**:

$$EF' = EF + (0.1 - (5 - q) \times (0.08 + (5 - q) \times 0.02))$$

* $q$: Quality of response (5: perfect, 4: correct after hesitation, 3: correct with difficulty, 2: wrong but remembered answer, 1: incorrect, 0: total blackout).
* $EF$: Ease factor (minimum bound: 1.3, initial default: 2.5).

### Mistake Queue Architecture

1. **Failure Logging:** When a learner answers an exercise incorrectly ($q < 3$):
   - A `learner_mistake` record is created with the exact chosen answer, correct answer, and timestamp.
   - The associated vocabulary, kanji, or grammar item's mastery score drops by 1 level.
2. **Automated Feed Injection:**
   - The system checks the `learner_mistakes` table daily.
   - An exercise addressing the missed concept is automatically injected into the learner's feed at intervals of **1 day**, **3 days**, and **7 days** until answered correctly twice consecutively.
3. **Diagnostic Mastery Report:** The learner's profile displays a breakdown of mastery across skills (e.g., *N4 Grammar: 82%, N4 Particles: 58% — Needs Review*).

---

## 18. Suggested Database Structure

A practical, relational schema designed for SQLite (MVP) and PostgreSQL (Production):

```sql
-- Central Vocabulary
CREATE TABLE vocabulary (
    id TEXT PRIMARY KEY,
    written_form TEXT NOT NULL,
    kana_reading TEXT NOT NULL,
    romaji TEXT,
    meanings TEXT NOT NULL,           -- JSON array of strings
    part_of_speech TEXT,
    jlpt_level TEXT CHECK(jlpt_level IN ('N5','N4','N3','N2','N1')),
    audio_path TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Central Kanji
CREATE TABLE kanji (
    id TEXT PRIMARY KEY,
    character TEXT UNIQUE NOT NULL,
    meanings TEXT NOT NULL,           -- JSON array
    onyomi TEXT NOT NULL,             -- JSON array
    kunyomi TEXT NOT NULL,            -- JSON array
    stroke_count INTEGER,
    jlpt_level TEXT CHECK(jlpt_level IN ('N5','N4','N3','N2','N1')),
    frequency_rank INTEGER,
    stroke_svg_data TEXT
);

-- Central Grammar
CREATE TABLE grammar (
    id TEXT PRIMARY KEY,
    pattern TEXT NOT NULL,
    meaning TEXT NOT NULL,
    formation TEXT NOT NULL,
    usage_notes TEXT,
    jlpt_level TEXT CHECK(jlpt_level IN ('N5','N4','N3','N2','N1'))
);

-- Feed Items
CREATE TABLE feed_items (
    id TEXT PRIMARY KEY,
    title TEXT NOT NULL,
    summary TEXT NOT NULL,
    content_type TEXT NOT NULL,       -- news, vocab_daily, grammar_tip, etc.
    jlpt_level TEXT NOT NULL,
    skill_category TEXT NOT NULL,
    body_json TEXT NOT NULL,          -- Japanese text, furigana, translation
    published_at TIMESTAMP NOT NULL,
    estimated_minutes INTEGER DEFAULT 3,
    source_attribution TEXT
);

-- Exercises
CREATE TABLE exercises (
    id TEXT PRIMARY KEY,
    feed_item_id TEXT REFERENCES feed_items(id),
    exercise_type TEXT NOT NULL,      -- vocab_choice, grammar_cloze, particle, etc.
    prompt TEXT NOT NULL,
    audio_url TEXT,
    options_json TEXT NOT NULL,       -- Array of 4 options
    correct_option_index INTEGER NOT NULL,
    explanation TEXT NOT NULL,
    target_vocab_id TEXT REFERENCES vocabulary(id),
    target_kanji_id TEXT REFERENCES kanji(id),
    target_grammar_id TEXT REFERENCES grammar(id)
);

-- Learner Progress & SRS State
CREATE TABLE learner_progress (
    id TEXT PRIMARY KEY,
    user_id TEXT NOT NULL,
    entity_type TEXT NOT NULL,        -- 'vocabulary', 'kanji', 'grammar'
    entity_id TEXT NOT NULL,
    repetition_count INTEGER DEFAULT 0,
    interval_days INTEGER DEFAULT 1,
    ease_factor REAL DEFAULT 2.5,
    next_review_date TIMESTAMP NOT NULL,
    consecutive_correct INTEGER DEFAULT 0,
    total_attempts INTEGER DEFAULT 0,
    total_failures INTEGER DEFAULT 0,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    UNIQUE(user_id, entity_type, entity_id)
);

-- Mistake Journal
CREATE TABLE learner_mistakes (
    id TEXT PRIMARY KEY,
    user_id TEXT NOT NULL,
    exercise_id TEXT NOT NULL REFERENCES exercises(id),
    selected_option_index INTEGER NOT NULL,
    resolved BOOLEAN DEFAULT FALSE,
    occurred_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

---

## 19. Features to Implement in Our Own Application

### Phase 1: MVP Learning Feed & Drills
- [x] Responsive layout with sticky top navigation and level switchers (N5–N1).
- [x] Level-filtered feed displaying Daily News Digests and Grammar Bites.
- [x] Reading view with interactive `<ruby>` furigana toggles (Show All / Kanji Only / Hidden).
- [x] Interactive 4-choice Multiple Choice and Cloze exercise runner.
- [x] Client-side score calculation, instant answer key feedback, and explanations.
- [x] LocalStorage persistence for guest quiz scores and review history.

### Phase 2: Personalization & Spaced Repetition
- [ ] User authentication (OAuth2 / email) with cloud progress sync.
- [ ] Automated SM-2 Spaced Repetition engine scheduling daily reviews.
- [ ] Dynamic Mistake Recovery feed module injecting previously failed questions.
- [ ] Audio player integration using high-quality Japanese neural TTS.
- [ ] 30-question adaptive Placement Test to recommend starter level.

### Phase 3: Advanced Testing & Community
- [ ] Timed full-length JLPT Mock Exams (N5–N1) with section-by-section scoring.
- [ ] Public and friend-group ranking leaderboards.
- [ ] Peer study rooms and speaking exchange integration.
- [ ] Printable PDF cheat-sheets (Hiragana, Katakana, Essential Kanji).

---

## 20. Further Research Required

1. **TTS Audio Latency & Naturalness:** Evaluate whether Web Speech API synthesis is sufficient or if pre-rendered Amazon Polly / Google Cloud TTS Japanese neural voices are required for listening comprehension passages.
2. **Copyrighted News Curation Workflows:** Formulate a legal ingestion pipeline for news: summarizing public Japanese press releases or re-writing news stories to guarantee 100% original prose while retaining educational value.
3. **Optimal Placement Test Algorithm:** Research Item Response Theory (IRT) computer-adaptive testing algorithms to shrink the 30-question placement test down to 15 questions while maintaining 90%+ level diagnostic accuracy.

---

## 21. Copyright, Licensing, and Attribution Considerations

### Target Site Attribution
* `https://www.minnanihongo.com/` is operated independently by the Minna Nihongo Editorial Team.
* All trademark rights to "Minna no Nihongo" belong to their respective commercial owners (3A Corporation). Our platform must **not** copy this trademark or brand identity.
* The target site sources simplified news from **NHK News Web Easy** (`https://www3.nhk.or.jp/news/easy/`).

### Clean-Room Content Sourcing for Our Platform
* **Vocabulary & Dictionaries:** Ingest from [JMdict / EDICT](http://www.edrdg.org/wiki/index.php/JMdict-EDICT_Dictionary_Project) under CC BY-SA 4.0.
* **Kanji Data & Radicals:** Ingest from [KANJIDIC2](http://www.edrdg.org/wiki/index.php/KANJIDIC_Project) under CC BY-SA 4.0.
* **Stroke Order SVG Geometry:** Ingest from [KanjiVG](https://kanjivg.tagaini.net/) under CC BY-SA 3.0.
* **Example Sentences:** Ingest open bilingual pairs from [Tatoeba](https://tatoeba.org/) under CC BY 2.0 FR.
* **Exercises & Explanations:** 100% authored independently by our team.

---

## 22. Implementation Recommendations and Next Steps

1. **Initialize Project Skeleton:** Set up Next.js 14+ (App Router) with TypeScript and Tailwind CSS.
2. **Construct Central Database:** Deploy SQLite schema locally using Drizzle ORM or Prisma.
3. **Ingest Open-Source Lexicon:** Execute seed scripts compiling JMdict N5/N4 subsets and KanjiVG SVGs.
4. **Implement Exercise Runner:** Build the interactive `ExerciseCard` component with radio selection, submission lock (50% threshold), feedback state, and explanation disclosure.
5. **Implement Feed View:** Create the level-filtered feed page with news cards, grammar tips, and review reminders.
6. **Integrate SM-2 Logic:** Connect exercise submission outcomes to update `learner_progress` records in LocalStorage.

---

*This document was compiled following direct verification of live web environments via REA (Reverse Engineer Anything).*  
*Project Root Path:* `D:\sofia.jp\RESOURCE.md`
