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
