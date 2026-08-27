# Fantasy Baseball Battle Royale

A browser-based 3D fighting game (Three.js) where the fighters are the
managers from a fantasy baseball league, each skinned in their team's
colors — pick a fighter and punch it out.

## How it works

- Built with [Three.js](https://threejs.org/) (r160), loaded via an
  `importmap` from a CDN — no build step, no bundler
- Character rigs and animations (`Idle`, `Punch`) are `.fbx` models loaded
  with Three's `FBXLoader`, retargeted per-character with
  `SkeletonUtils`
- Each fighter (Ted, Danny, Doug, Nate, Nick, Paul, Josh, Dan, Kevo, Hyle,
  Scherz) is built from a shared `makeCharacter()` template with its own
  name, home team color scheme, and gradient jersey texture
- Fighter select screen → touch/mouse-controlled match with health bars,
  a special-move meter, and round win indicators
- Mobile-friendly: `touch-action` tuned throughout, safe-area insets
  respected, no text selection/callouts during play

## Structure

```
FantasyBaseballBattleRoyale-main/
├── index.html          # entire game: markup, styles, and Three.js logic
├── *Idle.fbx            # per-fighter idle animation
├── *Punch.fbx            # per-fighter punch animation
└── *.fbx                 # base character rigs
```

## Running locally

Serve the folder rather than opening the file directly, since it loads
`.fbx` assets via relative paths and ES modules from a CDN import map:

```bash
python -m http.server
```

Then visit `http://localhost:8000/index.html`.

## Customizing: adding your own fighters

Each fighter needs **three `.fbx` files** — a base rig, an idle animation, and a punch animation — plus one entry in the `CHARACTERS` config in `index.html`. There's no separate manifest/allow-list to keep in sync in this project (unlike some other Three.js projects in this league) — the loader just tries to fetch whatever filename you give it and falls back to a placeholder capsule if it's missing or fails to load.

### 1. Generate a character model

Use a text/image-to-3D generator to create your fighter's base mesh:

- [Tripo](https://www.tripo3d.ai/)
- [Meshy](https://www.meshy.ai/)
- [Hunyuan3D](https://3d.hunyuan.tencent.com/)

Export the result as `.fbx` or `.glb`/`.obj` (whatever the rigger accepts — Mixamo takes `.fbx` and `.obj`).

### 2. Rig and animate it in Mixamo

Upload the model to [Mixamo](https://www.mixamo.com/):

1. Auto-rig the character (Mixamo places the skeleton for you).
2. Search the animation library and download **three** clips per character:
   - A base/idle pose or animation → your `*Idle.fbx`
   - A punch animation → your `*Punch.fbx`
   - The rigged character itself (T-pose or first animation you download, "With Skin") → your base `*.fbx`
3. Download each as **FBX (Binary)**, "With Skin" checked on at least the base rig download.

This hasn't been fully battle-tested — some models come in with quirks (wrong scale, feet floating or sinking, odd rotation) that need small hardcoded fixes in `index.html` for that specific model. See step 4.

### 3. Name and drop in the files

Pick a short file-base name for your fighter (no spaces), e.g. `Alex`. Name your three exported files to match exactly:

```
Alex.fbx        # base rig
AlexIdle.fbx    # idle animation
AlexPunch.fbx   # punch animation
```

Drop all three in the project root, next to `index.html`.

### 4. Register the fighter in code

Open `index.html` and find the `CHARACTERS` config (search for `const CHARACTERS = {`). Add a new entry using `makeCharacter()`:

```js
const CHARACTERS = {
  ...
  alex: makeCharacter(
    "Alex",              // file base — must match Alex.fbx / AlexIdle.fbx / AlexPunch.fbx
    "ALEX",               // fighter display name
    "Rockies",             // team name shown on their card
    "#a8353a",              // CSS accent color (UI)
    0x33006f,                // placeholder capsule color (hex, used if the model fails to load)
    "linear-gradient(160deg,#1a0033,#33006f 60%,#0a0015)"   // card background gradient
  )
};
```

The object key (`alex`) just needs to be unique — it isn't shown anywhere. `makeCharacter()`'s first argument (the file base) is what wires the entry to your three `.fbx` files, so it must match your filenames exactly, including capitalization.

If your model floats above the ground or sinks into it, adjust that character's `modelYOffset` (set it inside the object `makeCharacter()` returns, or add a one-off fix the way `Paul.fbx`/`Ted.fbx` do a few dozen lines below the model-loading code — search for `cfg.model ===` in `index.html`).

### Renaming an existing fighter

To rename one of the built-in fighters (e.g. rename "Ted" to something else) without changing their model, only the second argument to `makeCharacter()` (the display name) needs to change — the first argument (file base) can stay pointing at the existing `.fbx` files:

```js
ted: makeCharacter(
  "Ted",        // ← leave this alone if you're keeping Ted.fbx / TedIdle.fbx / TedPunch.fbx
  "T-BONE",      // ← this is what changes — the name shown in-game
  "Red Sox",
  ...
)
```

If you also want to swap in a new model for them, rename the `.fbx` files (or point the first argument at a different file base) and follow steps 1–3 above.
