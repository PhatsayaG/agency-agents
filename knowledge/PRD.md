# Product Requirements Document: NutriMon (營養獸) v1.0

## 1. Project Identity & Architecture

- [cite_start]**App Name**: NutriMon (營養獸) / Thai: นูทริม่อน[cite: 191, 192, 193].
- [cite_start]**Core Loop**: Users upload food images -> AI analyzes macros -> Data drives the physical evolution of a virtual elemental avatar[cite: 189].
- **Tech Stack**:
  - [cite_start]**Frontend**: Next.js (React), Tailwind CSS, Shadcn UI[cite: 195, 196].
  - [cite_start]**Mobile Wrapper**: Capacitor.js (Native iOS/Android)[cite: 197].
  - [cite_start]**Backend & Auth**: Supabase[cite: 198].
  - [cite_start]**Offline Storage**: IndexedDB via Dexie.js[cite: 199].
  - [cite_start]**AI Model**: Google Gemini 3 Flash Preview[cite: 206].

## 2. Database Schema (Supabase) MUST IMPLEMENT

### 2.1 Table: `Profiles`

- [cite_start]`avatar_element` (String): Earth, Water, Fire, or Wind[cite: 346].
- [cite_start]`avatar_growth_params` (JSONB): Stores current width, scale, opacity, and color/glow effects[cite: 222, 347].
- [cite_start]`daily_macro_stats` (JSONB): Records today's protein, carbs, and fat ratios[cite: 348]. [cite_start]MUST trigger immediate avatar transformation upon reaching specific thresholds[cite: 348].

### 2.2 Table: `Analysis_History`

- [cite_start]`image_hash` (String): Used for exact cache matching[cite: 349].
- [cite_start]`raw_image_url` (String): Supabase Storage URL of the compressed image[cite: 350].
- [cite_start]`analysis_result` (JSONB): The raw JSON output from the AI[cite: 350].
- [cite_start]`avatar_state_snapshot` (JSONB): Avatar's visual state at the time of the log[cite: 351].

## 3. Core Workflows (STRICT POLICIES)

### 3.1 Upload & Storage Pipeline

1. [cite_start]**Client-side Compression**: The frontend MUST compress uploaded images to a maximum width of 800px BEFORE any database or storage interaction[cite: 352]. [cite_start]This saves 80% of bandwidth[cite: 354].
2. [cite_start]**Offline-First Save**: The compressed image and pending state MUST be saved to IndexedDB (Dexie.js) first[cite: 201]. [cite_start]If offline, display a "Syncing..." placeholder UI[cite: 266].
3. [cite_start]**Background Sync**: Once online, automatically upload to Supabase Storage[cite: 202, 267].

### 3.2 AI Routing & Caching

- **API Security**: API keys MUST NOT be exposed in the frontend. [cite_start]All AI calls MUST route through Supabase Edge Functions with strict CORS policies[cite: 338, 339, 340].
- **Cache Hit Policy**: Before calling the AI, compute the image hash. [cite_start]Query `Analysis_History`[cite: 237, 238]. [cite_start]If a match exists and is < 30 days old, return the cached JSON immediately to bypass the AI API[cite: 239].

## 4. AI Prompt Interface

- [cite_start]**Role**: Professional nutritional analyst with a humorous closing tone based on the chosen element[cite: 304, 305].
- **Constraint**: Output MUST be pure JSON. [cite_start]NO Markdown formatting[cite: 314].
- **JSON Schema Target**:

```json
{
  "productName": "String",
  "verdict": { "title": "String", "color": "red|green|yellow" },
  "highlights": [
    { "type": "good|bad", "label": "String", "value": "String", "desc": "String" }
  ],
  "translations": [
    { "origin": "String", "simplified": "String", "explain": "String" }
  ],
  "advice": { "target": "String", "warning": "String", "action": "String" }
}

## 5. UI/UX & Avatar Engine (STRICT POLICIES)

### 5.1 Frontend Performance
- **Hardware Acceleration**: Lottie/Rive skeleton animations MUST force hardware acceleration in code for 60fps performance.

### 5.2 Elemental Styling & Macro Morphs
- **Earth**: Stable, brown/rock textures.
- **Water**: Gentle, translucent/liquid textures.
- **Fire**: Passionate, glowing/flame textures.
- **Wind**: Agile, floating particles.
- **Macro-driven Morphs**:
  - Carbs -> Increases horizontal width (roundness).
  - Protein -> Increases structural detail/definition.
  - Fat -> Triggers texture effects (glow, oil, smoke).

### 5.3 Localization & Sharing
- **Thai UI**: All UI components displaying Thai script MUST strictly apply a `1.5x` line-height utility to prevent font clipping.
- **Social Share**: Use Satori + Resvg for server-side generation of high-res shareable cards (Open Graph ready).
```
