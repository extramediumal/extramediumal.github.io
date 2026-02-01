# The Sorting Hat Quiz - V1 Design Document

**Date:** 2026-02-01
**Status:** Approved for implementation

---

## Overview

**The Sorting Hat Quiz** is a web and mobile app that delivers an authentic, replayable Hogwarts sorting experience. Unlike generic personality quizzes, it features a sassy, lore-accurate Sorting Hat character who delivers personalized commentary before revealing your house.

### Target Audience
Ages 10+ including Harry Potter fans of all ages

### Core Value Props
- **Aesthetic immersion** - Pixel art style (Noita-inspired), rich animations, atmospheric backgrounds, polished magical feel
- **Variety** - Hybrid question format pulled randomly from a growing library
- **Personality** - A Sorting Hat with attitude who roasts and charms users
- **Replayability** - Different questions each time
- **Shareability** - Generate images/links to show off your house

### V1 Scope
- Heavy investment in visual polish: pixel art assets, background scenes, transitions, particle effects
- ~30 curated questions at launch, randomizing ~15-20 per session
- Sorting Hat dialogue with typewriter text effect and animations
- Shareable result cards
- Audio: license-free music + sound effects (user-sourced)
- Anonymous play, no accounts
- Ephemeral results (share now or lose it)

### Project Structure
```
/sorting-hat-app        - Main Next.js application
/sorting-hat-research   - Local research database (not shipped)
```

### Pinned for Later
- Younger wizard mode (ages 6-9)
- Wand selection experience
- Voice acting for the Hat
- Expanded reveal ceremony animations

---

## Core User Flow

### Landing Screen
- Atmospheric pixel art scene: Great Hall at night, candles flickering, the Sorting Hat on its stool
- Ambient background music begins (if audio enabled)
- Single call-to-action: "Begin the Sorting Ceremony"
- Audio toggle visible but unobtrusive

### Introduction Sequence
- Brief animated transition (camera pushes toward the Hat, or Hat "awakens")
- Hat delivers opening quip: "Ah, another one. Let's see what you're made of..."
- Typewriter text effect with subtle sound
- User taps/clicks to proceed

### Quiz Phase
- System pulls 15-20 questions randomly from the library
- Mix of question types presented:
  - **This or That** - Two options, simple binary choice
  - **Multiple Choice** - 4 options, one clearly maps to each house (hidden from user)
  - **Scenario** - Short narrative situation, 2-4 response options
- Each question has a pixel art vignette or icon relevant to the scenario
- Smooth transitions between questions
- Optional: Hat interjects occasionally with commentary ("Interesting choice...")

### Sorting Sequence
- Final question answered, screen dims
- Hat "deliberates" - animated thinking, maybe wobbles or glows
- Personalized dialogue based on trait scores: "Plenty of courage... but that ambition, oh yes..."
- Dramatic pause, then house reveal with color flood, crest animation, and sound fanfare

### Result Screen
- House crest prominently displayed with pixel art flair
- Hat's final quip tailored to the house
- Share button generates image/link
- "Sort Again" option to replay with fresh questions
- Results are ephemeral - share now or start over

---

## Question System Architecture

### Question Storage
- Questions stored as JSON in the app codebase (no database for V1)
- Structured for easy additions - drop in new questions without code changes
- Each question is a self-contained object with all metadata

### Question Schema
```json
{
  "id": "unique-identifier",
  "type": "binary | multiple | scenario",
  "text": "The question or scenario prompt",
  "options": [
    {
      "text": "Option A",
      "traits": { "courage": 2, "ambition": 0, "wisdom": 1, "loyalty": 0 }
    }
  ],
  "asset": "reference to pixel art vignette (optional)",
  "hatInterjection": "occasional quip after answering (optional)"
}
```

### Randomization Logic
- Pool of ~30 questions at launch
- Each session pulls 15-20, ensuring mix of types
- Optional: tag questions by theme (friendship, conflict, knowledge) to ensure variety
- No repeat questions within a single session

### Extensibility
- Adding questions = adding JSON entries
- Research database feeds polished questions into the main app over time

---

## Sorting Logic & Traits

### The Four Traits
| Trait | House | Values |
|-------|-------|--------|
| Courage | Gryffindor | Bravery, nerve, daring, standing up for others |
| Ambition | Slytherin | Cunning, resourcefulness, determination, desire to prove oneself |
| Wisdom | Ravenclaw | Curiosity, wit, love of learning, creativity |
| Loyalty | Hufflepuff | Fairness, patience, dedication, kindness |

### Scoring Mechanics
- User starts at 0 for all traits
- Each answer adds points (typically 1-3) to one or more traits
- Some answers may boost two traits (e.g., a clever act of bravery adds to both Courage and Wisdom)
- Negative points are avoided - measuring presence, not absence

### Tie-Breaking
- If two houses tie, secondary factors decide:
  - Which trait scored higher earlier in the quiz (first impressions)
  - Or: random selection with Hat dialogue acknowledging the split
- Ties are rare with 15-20 questions but handled gracefully

### Hidden from User
- No progress bars, no "you're leaning Gryffindor" hints
- The black box maintains mystery
- Only the Hat's deliberation dialogue hints at what he saw

---

## Sorting Hat Dialogue System

### Dialogue Moments
| Moment | Purpose |
|--------|---------|
| Opening | Welcomes/teases the user as they sit down |
| Mid-quiz interjections | Occasional commentary after certain answers |
| Deliberation | Thinking out loud before reveal, referencing traits |
| Reveal | Announces the house with house-specific quip |
| Closing | Final send-off line on result screen |

### Dialogue Pool Structure
```json
{
  "moment": "opening",
  "variants": [
    "Ah, another one. Sit down, sit down... let's see what you're hiding.",
    "Oh, you think you know where you belong? We'll see about that.",
    "Hmm. You look nervous. Good. You should be."
  ]
}
```

### Personalized Deliberation
The deliberation dialogue is dynamically assembled based on trait scores:
- Top trait gets a compliment or observation
- Second trait gets a "but also..." acknowledgment
- Creates the feeling that the Hat actually *read* you

Example: "Brave, certainly... but that sharp mind of yours nearly sent you to Ravenclaw. In the end though... GRYFFINDOR!"

### Tone Guidelines
- Sassy but not mean
- Lore-accurate superiority complex
- Occasional fourth-wall winks okay ("Another protagonist type, I see...")
- Every house gets a dignified, fun reveal - no house treated as lesser

---

## Result & Sharing

### Result Screen Layout
- Full-screen house colors flood in with pixel art particle effects
- Animated house crest (pixel art, subtle shimmer or glow)
- House name in stylized magical font
- Hat's personalized closing quip below
- Two buttons: **Share** and **Sort Again**

### Share Functionality
- Tapping Share generates an image card:
  - House crest and colors
  - "I was sorted into [HOUSE]"
  - Hat's quip (optional toggle)
  - Subtle app branding/watermark for organic marketing
- Native share sheet (Web Share API / Capacitor share plugin)
- Fallback: download image directly if share API unavailable
- Optional: simple templated link per house (e.g., `sortinghat.app/share/gryffindor`)

### Sort Again
- Clears session, returns to landing screen
- Fresh question set pulled on next run
- No confirmation modal needed

---

## Tech Stack & Architecture

### Frontend
| Technology | Purpose |
|------------|---------|
| Next.js 14+ (App Router) | React framework with static export support |
| TypeScript | Type safety for schemas |
| Tailwind CSS | Utility-first styling |
| Framer Motion | Animations, transitions, particles |

### Audio
- **Howler.js** or native Web Audio API
- Audio sprites for efficiency

### Mobile Packaging
- **Capacitor** - Wrap web app for iOS and Android
- Native share plugin for share sheet integration
- Local storage plugin for settings persistence

### Project Structure
```
/sorting-hat-app
  /src
    /app          - Next.js pages and routes
    /components   - UI components (Hat, QuestionCard, ResultScreen, etc.)
    /data         - Question library JSON, dialogue pools JSON
    /lib          - Sorting logic, randomization, trait calculator
    /assets       - Pixel art, audio files, fonts
  /public         - Static assets
  capacitor.config.ts

/sorting-hat-research
  /scraped        - Raw content from web research
  /houses         - House-specific content (see below)
  /inspiration    - Screenshots, style references
  /lore           - General HP world facts
  /drafts         - WIP questions and dialogue
```

### Deployment
- **Web:** Vercel
- **iOS/Android:** Build via Capacitor, submit to stores

---

## Visual & Audio Design

### Pixel Art Style
- Inspired by Noita: detailed, atmospheric, slightly moody
- Not cutesy 8-bit - more like 16-bit/32-bit era with rich color palettes
- Consistent pixel density across all assets

### Key Visual Assets (V1)
| Asset | Description |
|-------|-------------|
| Landing scene | Great Hall at night, candles, Hat on stool |
| Hat character | Idle, thinking, and speaking animations |
| Question vignettes | Small scene or icon per question type |
| House crests | Pixel art versions of all four |
| Result backgrounds | One per house with themed elements |
| Particles/effects | Magic sparkles, candle flicker, color floods |

### Animation Priorities
1. Hat expressions and movement (the star of the show)
2. Smooth transitions between screens
3. Typewriter text with subtle timing variations
4. Result reveal moment - deserves extra polish

### Audio Design
- **Background music** - Atmospheric, magical, loopable (license-free, user-sourced)
- **Sound effects:**
  - Hat "hmm" / thinking sounds
  - UI clicks and swooshes
  - Question transition chimes
  - Reveal fanfare
- **Audio toggle** - Persisted in local storage

---

## Content Pipeline & Research Database

### Research Database Purpose
The `/sorting-hat-research` folder is the content workshop - raw materials, inspiration, and drafts that feed into the polished app over time. Never shipped.

### Folder Structure
```
/sorting-hat-research
  /scraped
    - Pottermore quiz breakdowns
    - Fan wiki house trait summaries
    - Existing quiz question patterns

  /houses
    /gryffindor
    /slytherin
    /ravenclaw
    /hufflepuff
    Each containing:
      - traits.md        - Core values, characteristics
      - history.md       - Founding, common room, ghost, etc.
      - notable-members.md - Canonical characters and why they fit
      - scenarios.md     - Situations where house values shine
      - aesthetics.md    - Colors, symbols, element, animal, vibe

  /inspiration
    - Screenshots, pixel art references, UI patterns

  /lore
    - General HP world facts useful for scenarios
    - Sorting Hat history and songs

  /drafts
    - WIP questions and dialogue
```

### House Content to Collect (Legally Safe Sources)
- **Fan wikis** (Harry Potter Wiki, Fandom) - Summaries and facts, not verbatim book text
- **Pottermore/Wizarding World articles** - House welcome letters, descriptions
- **Public interviews** - J.K. Rowling quotes about house traits
- **Fan analysis** - Blog posts, Reddit threads discussing house psychology
- **Your own interpretation** - Notes on what each house means to you

### What to Avoid
- Direct book excerpts (copyrighted)
- Movie scripts or dialogue
- Official artwork or logos (create original pixel art instead)

### Initial Research Tasks
1. Scrape house trait summaries from 3-4 fan wikis
2. Document each founder's story and values
3. List 10+ notable characters per house with sorting rationale
4. Collect house aesthetic details (colors, common room descriptions, etc.)
5. Gather 20+ existing quiz questions as format inspiration (not to copy)
6. Compile Sorting Hat song excerpts that describe houses

### Content Workflow
1. **Research** - Scrape and collect inspiration into `/scraped` and `/inspiration`
2. **Extract** - Pull useful patterns, traits, scenarios into `/lore` and `/drafts`
3. **Draft** - Write original questions and dialogue in `/drafts`
4. **Promote** - When polished, move questions into `/sorting-hat-app/src/data`

---

## Future Considerations (Pinned Ideas)

### Age Modes (V2)
- "Young Wizard" mode for ages 6-9
- Simpler language, more visual questions
- Age selection at start: "I'm a young wizard" vs "I'm ready for my O.W.L.s"

### Wand Selection Experience (V2+)
- Secondary quiz after sorting (or standalone)
- Wood type, core, length, flexibility
- Different personality model than house sorting
- Could share result alongside house

### Enhanced Reveal Ceremony (V2+)
- Full animation sequence
- Hat voice acting
- House-specific music/fanfare
- More elaborate particle effects

### Additional Experiences (Future)
- Patronus quiz
- Ilvermorny sorting (American wizarding school)
- "Which character are you?" quiz
- Trivia mode using lore database

### Social/Account Features (If Needed)
- Family profiles
- Result history
- Compare with friends
- Leaderboards for trivia mode

### Monetization (If Relevant)
- Ad-supported free version
- One-time purchase for ad-free
- Cosmetic themes (different pixel art styles)
