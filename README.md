# Tower of IE: The Wizard Climb

<p align="center">
  <b>A 2D fantasy vertical platformer built with Python & Pygame — playable in the browser</b><br>
  Deployed on itch.io via WebAssembly (Pygbag)
</p>

---

## Play Online

**[Play on itch.io](https://stefhooy.itch.io/tower-of-ie-the-wizard-climb)**

No installation required. Runs directly in your browser.

> For the best display, use your browser's zoom (Ctrl + / Ctrl -) until the game fills your screen.

---

## Game Overview

**Tower of IE: The Wizard Climb** is a 2D vertical platformer where you play as a sorcerer student at IE University. Conjure platforms to climb the tower and reach the flag at the top as fast as possible!

The game features real-time physics, collision detection, dynamic platform creation (editor mode), persistent score tracking, and looping background music.

---

## How to Play

| Key | Action |
|-----|--------|
| Arrow keys / WASD | Move and jump |
| E | Toggle editor mode |
| [ / ] | Decrease / increase platform width (editor) |
| - / + (numpad) | Decrease / increase platform height (editor) |
| Left click | Place platform (editor) |
| Right click | Remove nearest platform (editor) |
| R | Restart run (clears platforms) |
| S | View scoreboard (after finishing) |
| ESC | Return to menu |

---

## Architecture

### Core Components

**Player** — Movement, gravity, velocity, jumping, sprite rendering, collision detection.

**Platform** — Static surfaces the player can land on; created/removed in editor mode.

**Camera** — Dynamic vertical scrolling; transforms world coordinates to screen coordinates.

**GameApp** — Main loop, state machine (splash → menu → name input → game → scoreboard), rendering pipeline.

**Score System** — Stores completion times in a JSON file, ranked by fastest time.

**Audio System** — Initializes `pygame.mixer` and plays looping background music.

---

## Project Structure

```text
tower-of-ie-wizard-climb/
│
├── assets/                 # Backgrounds, sprites, fonts, music, scores.json
│
├── game/                   # Main package
│   ├── __init__.py
│   ├── settings.py
│   ├── utils.py
│   ├── scores.py
│   ├── effects.py
│   ├── audio.py
│   ├── camera.py
│   ├── platform.py
│   ├── player.py
│   ├── screens.py
│   └── app.py
│
├── build/web/              # Pygbag web build output
│   ├── index.html          # Patched HTML wrapper (do not overwrite without re-applying patches)
│   ├── video_game_2.tar.gz # Packaged Python game files
│   └── browserfs.min.js    # Bundled locally (CDN blocked by itch.io)
│
├── main.py                 # Entry point (asyncio.run for Pygbag compatibility)
├── pygbag.ini              # Pygbag build config
├── pyproject.toml
└── README.md
```

---

## Web Build

The game is compiled to WebAssembly using [Pygbag](https://pygame-web.github.io/) 0.9.3.

### Build command

```bash
python -m pygbag --build .
```

This regenerates `build/web/index.html` and `build/web/video_game_2.tar.gz`.

### After every build, re-apply these patches to `build/web/index.html`

1. **Line 1** — Add `devicePixelRatio=1` fix and set `data-os="vtx,gui"` (no `snd`)
2. **Tarfile block** — Use `mode="r:*"` and remove the itch.zone APK detection block
3. **embed.counter() loop** — Add 300-tick timeout to prevent infinite stall
4. **UME block** — Remove entirely (no audio WASM loaded)
5. **JS config** — Set `autorun:1`, `gui_divider:1`, `fb_width:"960"`, `fb_height:"540"`
6. **CSS** — Canvas `width:100vw`, `height:100vh`, `outline:none`; body `background:black`
7. **`custom_postrun`** — Add delayed resize events (500ms, 1500ms) to fix canvas sizing
8. **`browserfs.min.js`** — Use local copy, not CDN

### Package for itch.io upload

```bash
# From build/web/
Compress-Archive -Path index.html,favicon.png,video_game_2.tar.gz,browserfs.min.js -DestinationPath ../../tower-of-ie-web.zip -Force
```

Upload `tower-of-ie-web.zip` to itch.io. Set viewport to **960 x 540**.

---

## Technical Highlights

- Object-Oriented Python with modular package architecture
- Game loop design with async/await for browser compatibility
- Physics simulation (gravity, velocity, delta-time movement)
- Collision detection system
- Camera abstraction (world-to-screen transformation)
- JSON score persistence
- Background music via `pygame.mixer`
- WebAssembly deployment via Pygbag

---

## Author

Stephan Pentchev
Master's in Business Analytics and Data Science — IE University

Developed as part of a Python object-oriented programming coursework project.