# NorskSikhi 🇳🇴
### Norwegian Language Learning Platform for Marathi Speakers
**A1 → C2 | Songs | AI Tutor | Gamification | Fully Deployable**

---

## 🏗️ Architecture Overview

```
norsk-sikhi/
├── src/
│   ├── engine/
│   │   └── curriculumEngine.js     ← THE BRAIN: All topic taxonomy,
│   │                                   vocab seeds, song generator,
│   │                                   practice generator, AI prompts
│   ├── components/
│   │   ├── layout/
│   │   │   ├── TopBar.js           ← XP + streak bar
│   │   │   └── BottomNav.js        ← 6-tab navigation
│   │   ├── lessons/
│   │   │   ├── HomeScreen.js       ← Dashboard + quick start
│   │   │   ├── LessonsScreen.js    ← All levels topic browser
│   │   │   └── LessonDetail.js     ← Full lesson (7 tabs)
│   │   ├── practice/
│   │   │   └── PracticeScreen.js   ← MCQ + Flashcard + Translate
│   │   ├── songs/
│   │   │   └── SongsScreen.js      ← Built-in + AI-generated songs
│   │   ├── ai-tutor/
│   │   │   └── AITutor.js          ← Claude API chat (EN + Marathi)
│   │   └── gamification/
│   │       └── ProgressScreen.js   ← XP, badges, level roadmap
│   ├── hooks/
│   │   └── useUserState.js         ← XP, streaks, localStorage
│   └── App.js                      ← Routing + state
├── vercel.json                     ← Vercel deployment config
├── netlify.toml                    ← Netlify deployment config
└── .env.example                    ← API keys template
```

---

## 🧠 Curriculum Engine Design

The `curriculumEngine.js` is a **generative system**, not a static list.

| Component | What it does |
|-----------|-------------|
| `TOPIC_TAXONOMY` | 60+ topics organized A1→C2 across all life domains |
| `VOCAB_SEEDS` | Starter vocabulary per topic (AI expands on demand) |
| `SENTENCE_TEMPLATES` | Real-life sentences per topic |
| `GRAMMAR_NOTES` | Level-appropriate grammar with Marathi tips |
| `SONG_SEEDS` | Pre-built songs for core A1 topics |
| `IMAGE_MAP` | Visual associations + Unsplash search terms |
| `generateMCQ()` | Creates quiz from any vocab array automatically |
| `generateFillBlanks()` | Creates fill-in exercises from any sentences |
| `generateFlashcards()` | Creates flashcard set from any vocab |
| `generateTutorSystemPrompt()` | Creates perfect AI prompt for current level/topic |

**To add a new topic:** Add it to `TOPIC_TAXONOMY`, seed it in `VOCAB_SEEDS`, and the entire app (quiz, flashcards, lessons, practice) auto-generates for it.

---

## 🌐 DEPLOYMENT GUIDE

### Option A: Vercel (Recommended — Fastest, Free)

**Step 1: Push to GitHub**
```bash
# Install Git if not installed
git init
git add .
git commit -m "Initial NorskSikhi commit"

# Create a new repo on github.com, then:
git remote add origin https://github.com/YOUR_USERNAME/norsk-sikhi.git
git branch -M main
git push -u origin main
```

**Step 2: Deploy on Vercel**
1. Go to https://vercel.com and sign up with GitHub
2. Click "New Project"
3. Import your `norsk-sikhi` repository
4. Framework Preset: **Create React App**
5. Click **Deploy**
6. ✅ Your live URL: `https://norsk-sikhi.vercel.app`

**Step 3: Add Environment Variables on Vercel**
1. Go to your project → Settings → Environment Variables
2. Add: `REACT_APP_ANTHROPIC_KEY` = your key
3. Add: `REACT_APP_UNSPLASH_KEY` = your key
4. Redeploy

---

### Option B: Netlify

**Step 1: Push to GitHub** (same as above)

**Step 2: Deploy on Netlify**
1. Go to https://netlify.com and sign up
2. Click "Add new site" → "Import an existing project"
3. Connect GitHub → select `norsk-sikhi`
4. Build command: `npm run build`
5. Publish directory: `build`
6. Click **Deploy site**
7. ✅ Your live URL: `https://norsk-sikhi.netlify.app`

---

### Option C: Render (With Backend Proxy — Most Secure)

Use this if you want to keep your Anthropic API key server-side.

**Step 1: Create a simple Express proxy** (`server/index.js`):
```javascript
const express = require('express');
const app = express();
app.use(express.json());

app.post('/api/chat', async (req, res) => {
  const response = await fetch('https://api.anthropic.com/v1/messages', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'x-api-key': process.env.ANTHROPIC_KEY,  // Server-side only!
      'anthropic-version': '2023-06-01',
    },
    body: JSON.stringify(req.body),
  });
  const data = await response.json();
  res.json(data);
});

app.listen(3001);
```

**Step 2:** In `AITutor.js`, change the fetch URL from `https://api.anthropic.com/v1/messages` to `/api/chat`

**Step 3:** Deploy on https://render.com — free tier available.

---

## ⚡ Quick Local Setup

```bash
# Clone and run locally
cd norsk-sikhi
npm install
cp .env.example .env
# Edit .env with your API keys
npm start

# Opens at http://localhost:3000
```

---

## 🔑 API Keys You Need

| Key | Purpose | Where to get | Cost |
|-----|---------|--------------|------|
| Anthropic | AI Tutor (Claude) | console.anthropic.com | Pay per use (~$0.003/msg) |
| Unsplash | Real Norwegian photos | unsplash.com/developers | Free (50 req/hr) |

**Without API keys:** The app works in offline/demo mode with built-in vocabulary, songs, and practice exercises.

---

## 🚀 Scaling the Platform

### To add more vocabulary for any topic:
```javascript
// In curriculumEngine.js → VOCAB_SEEDS
VOCAB_SEEDS.your_new_topic = [
  { no: "Norwegian", en: "English", mr: "मराठी", pron: "pronunciation" },
  // Add as many as needed
];
```

### To add a new topic to the curriculum:
```javascript
// In TOPIC_TAXONOMY under the right level:
{ id: "new_topic", icon: "🎯", name: "Topic Name", domain: "domain" }
```

### To add a pre-built song for a topic:
```javascript
// In SONG_SEEDS:
new_topic: {
  title: "Song Title 🎵",
  style: "Happy upbeat",
  tempo: "120 BPM",
  purpose: "Memorize X vocabulary",
  lyrics: `Your lyrics here`,
}
```

### Recommended next features:
1. **Supabase** — Add user accounts, cross-device sync, leaderboards
2. **Firebase Auth** — Google/Apple sign-in
3. **Expo (React Native)** — Convert to iOS/Android app in ~2 days
4. **Audio recording + AI feedback** — Use Web Audio API + Whisper API
5. **Spaced Repetition (SRS)** — Implement Anki-style review scheduling
6. **Norwegian TTS** — Google Text-to-Speech API has Norwegian (nb-NO)

---

## 📊 Topic Coverage

| Domain | A1 | A2 | B1 | B2 | C1 | C2 |
|--------|----|----|----|----|----|----|
| Daily Life | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| Food & Kitchen | ✅ | ✅ | ✅ | - | - | - |
| Work & Office | - | - | ✅ | ✅ | ✅ | ✅ |
| Health & Emergency | ✅ | ✅ | ✅ | ✅ | ✅ | - |
| Travel & Transport | - | ✅ | ✅ | ✅ | - | - |
| Shopping | ✅ | ✅ | - | - | - | - |
| Academic | - | - | - | ✅ | ✅ | ✅ |
| Professional | - | - | - | ✅ | ✅ | ✅ |

**Total: 60+ topics seeded, infinite expansion via AI tutor**
