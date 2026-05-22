# 🌸 Chloe's Counting Garden — Game Plan
**Target:** Copilot (Claude) in VS Code  
**Player:** Chloe, age 4–5, learning number sequences 1–10  
**Goal:** Help her tap numbers in the correct order without skipping  

---

## 📋 Project Overview

| Property | Value |
|---|---|
| Game Name | Chloe's Counting Garden |
| Platform | Browser (HTML5) — single `index.html`, PWA-ready for iPad |
| Tech Stack | Vanilla HTML + CSS + JavaScript, no frameworks |
| Visual Style | Bright, pastel, magical garden — large buttons, big numbers |
| Learning Goal | Count 1→10 in order without skipping, recognise numerals |

---

## 🆓 Free & Open Source Assets to Use

### Fonts (Google Fonts — free, open source)
```html
<link href="https://fonts.googleapis.com/css2?family=Baloo+2:wght@800;900&family=Nunito:wght@700;800&display=swap" rel="stylesheet">
```
- **Baloo 2** — for the big number labels on flowers (rounded, playful)
- **Nunito** — for instructions and celebration text

### Icons (Iconify Fluent Emoji — MIT licence, free)
```html
<script src="https://code.iconify.design/iconify-icon/2.1.0/iconify-icon.min.js"></script>
```
Used for decorative elements:
- `fluent-emoji:butterfly` — floats out on correct tap
- `fluent-emoji:sunflower` — base decoration
- `fluent-emoji:sparkles` — hint indicator
- `fluent-emoji:party-popper` — win screen
- `fluent-emoji:star` — score / progress

### Celebration Confetti (js-confetti — MIT licence)
```html
<script src="https://cdn.jsdelivr.net/npm/js-confetti@0.12.0/dist/js-confetti.browser.js"></script>
```
Used on level complete — fire confetti with custom emojis 🌸🦋⭐

### Sound (Web Audio API — zero dependencies, built-in browser)
All sounds are generated procedurally. No audio files, no CDN needed.

### Speech (Web Speech API — built-in browser)
Used to speak numbers aloud: `speechSynthesis.speak(new SpeechSynthesisUtterance("3"))`.
This is the most important learning tool — Chloe hears the number as she taps it.

### Visuals (Inline SVG + CSS Animations — zero dependencies)
All flowers, petals, stems and garden elements are drawn with SVG and CSS.
No external image files needed — everything renders in the browser.

---

## 🎮 Core Game Loop

```
Title Screen (Chloe's name, big START button)
     ↓
Garden Screen — 10 closed flower buds scattered around
Each bud shows a number (1–10), randomly positioned
     ↓
Chloe taps the correct next number (must tap 1 first, then 2, etc.)
     ↓
CORRECT TAP:
  - Flower blooms (CSS animation, petals expand)
  - Butterfly flutters out
  - Voice says the number aloud ("One!", "Two!", etc.)
  - Sparkle particle burst
  - Bud fades to a fully bloomed flower
     ↓
WRONG TAP:
  - Flower wiggles/shakes gently (no harsh buzzer)
  - Soft "whoops" sound
  - Gentle voice: "Try again! What comes after [N]?"
  - No lives lost — infinite tries, pure encouragement
     ↓
HINT SYSTEM (auto-triggers after 5 seconds of no tap):
  - The correct flower gently glows and pulses
  - A little star bounces above it
     ↓
ALL 10 BLOOMED:
  - Garden fills with colour
  - Confetti rains down (js-confetti with 🌸🦋⭐)
  - Animation plays — butterflies dance across screen
  - Voice: "Amazing Chloe! You counted all the way to ten!"
  - Score card shows how many tries it took
  - "Play Again 🌸" button appears
```

---

## 🌸 Visual Design

### Layout
```
┌────────────────────────────────────────────┐
│  🌸 Chloe's Counting Garden       ⭐⭐⭐⭐  │
│  "Tap the flowers in order! 1, 2, 3..."    │
│                                            │
│     🌷②      🌷⑦      🌷④               │
│                                            │
│  🌷⑤           🌷①       🌷⑨            │
│                                            │
│     🌷③      🌷⑧      🌷⑥    🌷⑩        │
│                                            │
│         Next: tap  [ 1 ]  ← hint box      │
└────────────────────────────────────────────┘
```

### Flower States (CSS driven)
| State | Appearance |
|---|---|
| **Waiting** | Small green bud, number label on it |
| **Correct, blooming** | Petals expand with CSS keyframes (scale 0→1), bright colour |
| **Wrong tap** | Wiggle/shake animation (CSS `@keyframes shake`) |
| **Hinting** | Gentle pulse glow, star icon bounces above |
| **Fully bloomed** | Large open flower, butterfly hovers beside it |

### Flower Colours (one per number, consistent)
```js
const flowerColors = {
  1: '#FF6B8A',  // pink
  2: '#FF9F45',  // orange
  3: '#FFD700',  // yellow
  4: '#7FE07F',  // green
  5: '#4FC3F7',  // sky blue
  6: '#7C83FD',  // purple-blue
  7: '#CE93D8',  // lavender
  8: '#F48FB1',  // rose
  9: '#80DEEA',  // teal
  10: '#FFCC80', // peach
};
```

---

## 🏗️ Architecture

### 1. Game States
```js
const STATE = {
  TITLE:      'title',      // welcome screen
  PLAYING:    'playing',    // active game
  HINT:       'hint',       // auto-hint glowing
  CELEBRATING:'celebrating',// level win animation
  SCORE:      'score',      // end screen
};
```

### 2. Flower Object
```js
{
  number: 5,          // 1–10
  x: 0.3,            // position as % of screen width (responsive)
  y: 0.55,           // position as % of screen height
  state: 'bud',      // 'bud' | 'blooming' | 'bloomed' | 'shaking' | 'hinting'
  animationProgress: 0,
  element: null,      // DOM reference to the flower div
}
```

### 3. Positions
Flowers are placed using a pre-defined grid of 10 positions scattered
naturally (not in a grid), so the layout feels organic like a real garden.
Positions are defined as percentage-based `{x, y}` pairs so they scale
to any screen size (phone or iPad).

```js
const POSITIONS = [
  {x:15, y:25}, {x:50, y:15}, {x:80, y:30},
  {x:25, y:55}, {x:65, y:45}, {x:85, y:65},
  {x:10, y:72}, {x:42, y:70}, {x:70, y:75},
  {x:50, y:85},
];
// Shuffle which number goes to which position each new game
```

---

## ✨ Animation Specs

### Flower Blooming (CSS keyframes)
```css
@keyframes bloom {
  0%   { transform: scale(0.3) rotate(-20deg); opacity: 0.5; }
  60%  { transform: scale(1.2) rotate(5deg); }
  80%  { transform: scale(0.95) rotate(-2deg); }
  100% { transform: scale(1) rotate(0deg); opacity: 1; }
}
```

### Wrong Tap Shake
```css
@keyframes shake {
  0%, 100% { transform: translateX(0); }
  20%       { transform: translateX(-10px) rotate(-5deg); }
  40%       { transform: translateX(10px) rotate(5deg); }
  60%       { transform: translateX(-6px); }
  80%       { transform: translateX(6px); }
}
```

### Hint Pulse
```css
@keyframes hint-pulse {
  0%, 100% { box-shadow: 0 0 0 0 rgba(255,215,0,0.6); }
  50%       { box-shadow: 0 0 0 18px rgba(255,215,0,0); }
}
```

### Butterfly Float (appears on bloom)
- Created as a small `<div>` with 🦋 emoji
- Animated with CSS: floats up and fades out over 1.5s
- One butterfly per correct flower

---

## 🔊 Sound System (Web Audio API — no files needed)

```js
const ctx = new (window.AudioContext || window.webkitAudioContext)();

// Correct tap — bright rising chime
function playCorrect(noteIndex) {
  const notes = [262,294,330,349,392,440,494,523,587,659]; // C4 to E5
  const freq = notes[noteIndex % notes.length];
  const osc = ctx.createOscillator();
  const gain = ctx.createGain();
  osc.type = 'sine';
  osc.frequency.value = freq;
  gain.gain.setValueAtTime(0.4, ctx.currentTime);
  gain.gain.exponentialRampToValueAtTime(0.001, ctx.currentTime + 0.5);
  osc.connect(gain); gain.connect(ctx.destination);
  osc.start(); osc.stop(ctx.currentTime + 0.5);
}

// Wrong tap — soft low "boing"
function playWrong() { /* descending soft tone */ }

// Win fanfare — arpeggiated chime sequence
function playWin() { /* ascending 5-note sequence with delay */ }
```

### Speech (Web Speech API)
```js
function sayNumber(n) {
  const words = ['','One','Two','Three','Four','Five',
                 'Six','Seven','Eight','Nine','Ten'];
  const utter = new SpeechSynthesisUtterance(words[n]);
  utter.rate = 0.85;   // slightly slower for kids
  utter.pitch = 1.2;   // cheerful pitch
  speechSynthesis.speak(utter);
}

// Also say encouragement on correct tap
const praise = ['Great!','Amazing!','Yes!','Wonderful!','Perfect!'];
```

---

## 📊 HUD Elements

### Top Bar
- Left: `🌸 Chloe's Counting Garden` (title)
- Right: Stars earned (⭐ filled in as flowers bloom, 1–10)

### Hint Box (bottom centre)
A rounded pill showing:
```
Next: tap  ❷  ← updates as each number is tapped
```
This is always visible — gives Chloe a constant reference so she doesn't get lost.

### Progress Flowers (bottom left)
Row of 10 tiny flower icons that fill in as each number is tapped.

---

## 🎉 Win / Celebration Screen

1. js-confetti fires: `confetti.addConfetti({ emojis: ['🌸','🦋','⭐','🌼','✨'] })`
2. All flowers gently sway in a CSS wind animation
3. Full-screen overlay fades in with:
   - Big animated text: `"You did it, Chloe! 🎉"`
   - Sub-text: `"You counted all the way to 10!"`
   - Stars earned display
   - `"Play Again 🌸"` button
4. Voice says: `"Amazing! You counted to ten! You're a superstar Chloe!"`

---

## 📈 Difficulty Progression

This is a single-difficulty game for 4–5 year olds. Replayability comes from:
- Numbers are placed in **random positions** every new game
- After completing 1–10, a new round starts with a shuffled layout
- Optional: after 3 wins, introduce **1–15** or **1–20** mode

---

## 📱 PWA + Touch

```html
<!-- In <head> -->
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
<link rel="manifest" href="manifest.json">
```

```json
// manifest.json
{
  "name": "Chloe's Counting Garden",
  "short_name": "CountingGarden",
  "start_url": "./index.html",
  "display": "fullscreen",
  "orientation": "portrait",
  "background_color": "#d4f5d0",
  "theme_color": "#7FE07F",
  "icons": [
    { "src": "icon-192.png", "sizes": "192x192", "type": "image/png" },
    { "src": "icon-512.png", "sizes": "512x512", "type": "image/png" }
  ]
}
```

Touch targets are minimum **100×100px** — large enough for small fingers.
All tap events use `touchstart` + `click` with `e.preventDefault()`.

---

## 🚀 Build Order for Copilot

Build in this sequence for best results. Each step is a focused prompt:

### Step 1 — Scaffold & Title Screen
```
Create a single index.html for "Chloe's Counting Garden".
Include Google Fonts (Baloo 2, Nunito), Iconify CDN, js-confetti CDN.
Build a full-screen title screen with:
- Pastel green gradient background
- Big title "🌸 Chloe's Counting Garden" in Baloo 2
- Subtitle "Tap the flowers 1, 2, 3... in order!"
- A large animated START button
- 3–4 decorative butterfly emojis floating with CSS animation
Game states: TITLE, PLAYING, CELEBRATING, SCORE.
```

### Step 2 — Garden Layout & Flower Buds
```
Add 10 flower bud elements to the garden screen.
Each bud is a rounded div showing its number (1–10) in large Baloo 2 font.
Use these 10 pre-defined percentage positions: 
  [{x:15,y:25},{x:50,y:15},{x:80,y:30},{x:25,y:55},{x:65,y:45},
   {x:85,y:65},{x:10,y:72},{x:42,y:70},{x:70,y:75},{x:50,y:85}]
Shuffle which number gets which position on each new game.
Style buds as green rounded buttons, min 100x100px touch targets.
Bottom bar shows: "Next: tap [1]" hint pill.
```

### Step 3 — Tap Logic & Sequence Checking
```
Add tap/click handlers to each flower bud.
Track currentTarget (starts at 1, increments on each correct tap).
CORRECT tap (number === currentTarget):
  - Mark flower as 'bloomed', add 'bloomed' CSS class
  - Increment currentTarget
  - Update the "Next: tap [N]" hint
  - Check if all 10 are bloomed → trigger win
WRONG tap (number !== currentTarget):
  - Add 'shake' CSS class to that flower, remove after 600ms
  - Do NOT change currentTarget
```

### Step 4 — Bloom Animation & Butterflies
```
When a flower is marked 'bloomed':
- Apply the 'bloom' CSS keyframe animation (scale 0.3→1 with bounce)
- Change the bud's background to its unique colour from flowerColors[]
- Spawn a butterfly div (🦋 emoji) that floats upward and fades out
  using CSS animation over 1.5 seconds, then remove from DOM
Add the 'shake' keyframe for wrong taps.
Add the 'hint-pulse' keyframe for the hint system.
```

### Step 5 — Sound & Speech
```
Add Web Audio API for sounds:
- playCorrect(n): rising note from C major scale, index n (0-9)
- playWrong(): soft descending "boing"
- playWin(): 5-note ascending fanfare with 120ms delays between notes
Add Web Speech API:
- sayNumber(n): speak "One", "Two"... etc at rate=0.85, pitch=1.2
- After a correct tap, say the number spoken clearly
- On wrong tap, say "Try again! What comes after [N-1]?"
Resume AudioContext on first user tap (required by browsers).
```

### Step 6 — Hint System
```
Start a 5-second timer after each correct tap (or game start).
If no tap occurs in 5 seconds:
  - Add 'hinting' class to the correct flower (currentTarget)
  - This triggers the hint-pulse CSS glow animation
  - Spawn a bouncing ⭐ above that flower
Reset the timer whenever any tap occurs (correct or wrong).
```

### Step 7 — Win Celebration
```
When currentTarget reaches 11 (all 10 tapped):
- Switch to CELEBRATING state
- Fire js-confetti: confetti.addConfetti({emojis:['🌸','🦋','⭐','🌼','✨'], confettiNumber:80})
- All bloomed flowers start a gentle CSS sway animation
- Fade in celebration overlay with:
    "You did it, Chloe! 🎉"
    "You counted all the way to 10!"
    Row of 10 ⭐ stars
    "Play Again 🌸" button (resets and reshuffles)
- Call speechSynthesis: "Amazing! You counted to ten! You are a superstar Chloe!"
```

### Step 8 — Background & Polish
```
Garden background: soft pastel green radial gradient with:
- Decorative grass/ground strip at bottom (CSS)
- 2–3 floating cloud divs (white, animated drift)
- A small animated sun in top corner (CSS rotation glow)
- A bee emoji (🐝) that lazily flies a figure-8 path in the background
  using CSS animation (decorative only, not tappable)
Make everything responsive: test at 375px (iPhone SE) and 768px (iPad).
Ensure no layout overflow on small screens.
```

### Step 9 — PWA Manifest
```
Add to <head>:
  <meta name="apple-mobile-web-app-capable" content="yes">
  <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
  <link rel="manifest" href="manifest.json">
Create manifest.json with:
  name: "Chloe's Counting Garden"
  display: fullscreen, orientation: portrait
  background_color: #d4f5d0, theme_color: #7FE07F
Generate a simple programmatic icon (canvas-drawn flower) for icon-192.png fallback,
or note that the user needs to provide icon-192.png and icon-512.png.
```

### Step 10 — Final QA Checklist
```
Verify:
✅ Numbers always appear in correct positions after shuffle
✅ Tapping 3 before 1 or 2 does NOT advance the counter
✅ Hint glow appears after 5 seconds of inactivity
✅ Speech fires correctly for each number
✅ AudioContext resumes on first tap (no silent sounds)
✅ Celebration fires only after ALL 10 are tapped
✅ "Play Again" reshuffles positions correctly
✅ Touch targets ≥ 100×100px on mobile
✅ No layout overflow on 375px width
✅ Works offline after first load (add basic service worker if time allows)
```

---

## 🧠 Pedagogy Notes

These design choices are intentional for 4–5 year learning:

| Design Choice | Why It Helps |
|---|---|
| Number always SPOKEN aloud on tap | Connects numeral symbol → spoken word |
| "Next: tap [N]" always visible | Reduces frustration, child stays engaged |
| Hint auto-appears after 5s | No time pressure, no failure state |
| No lives / no timer | Pure exploration, no anxiety |
| Same number = same colour every game | Builds colour-number association over time |
| Random positions each game | Trains recognition (not just spatial memory) |
| 10 flowers exactly = fingers on hands | Natural physical anchor for the count |
| Praise is specific ("You counted to TEN!") | More meaningful than generic "Good job" |

---

*Built with 💖 for Chloe. All assets MIT/CC0/free. No paid subscriptions required.*