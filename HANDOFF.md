# Jumping Frog (`הצפרדע הקופצת`) — Project Handoff & Context

## 1. Project Overview

* **Concept:** A single-file, zero-dependency 2D HTML5 Canvas web game inspired by a hand-drawn sketch of a frog jumping across lily pads over a blue watercolor pond to reach a red-and-white goal flag.
* **Target Audience:** Kids (Hebrew-speaking interface, `dir="rtl"` layout, playful hand-drawn watercolor notebook aesthetic).
* **Repository & Hosting:** Hosted on GitHub Pages at `https://github.com/EliShayGH/jumping-frog` (Live URL: `https://elishaygh.github.io/jumping-frog/`).
* **Architecture:** 100% self-contained inside a single `index.html` file (HTML, CSS, Canvas 2D procedural watercolor & ink rendering, parabolic trajectory physics, and Web Audio API sound synthesis).

## 2. Core Gameplay Mechanics

* **Aiming & Trajectory Arrow (`drawAimingArrow`):**
  * **Up (`↑` / `W` / On-screen `חזק +`):** Increases jump power (`aimPower`), lengthening the colored trajectory arrow and extending the parabolic preview dots.
  * **Down (`↓` / `S` / On-screen `חלש -`):** Decreases jump power, shortening the arrow for closer lily pads.
  * **Right (`→` / `D` / On-screen `ימינה`):** Rotates the arrow clockwise (lower, flatter arc down to `14°`).
  * **Left (`←` / `A` / On-screen `שמאלה`):** Rotates the arrow counter-clockwise (higher, steeper arc up to `78°`).
  * **Launch (`Space` / `Enter` / On-screen `🦘 קפוץ!`):** Launches the frog with velocity `(cos(rad) * aimPower, -sin(rad) * aimPower)` under `GRAVITY = 980`.
* **Lily Pads, Hazards & Camera:**
  * Landing safely on a green lily pad restores jumping and updates `frog.safePadIndex`.
  * Missing a lily pad triggers a water splash (`triggerSplash()`) and returns the frog to the last safe lily pad without losing progress.
  * Higher levels (`level >= 3`) introduce gently drifting lily pads (`moveAmpY`, `moveAmpX`) and wider horizontal camera scrolling (`cameraX`).
* **Coins & Goal Flag:**
  * Intermediate and bonus side lily pads hold **`1`** or **`5`** coins.
  * Reaching the large goal lily pad with the red-and-white flag collects the **`10`** coin, awards 1–3 stars based on jump count, banks collected coins into `localStorage`, and advances the level.

## 3. Customization Store & Economy

* **Persistent Save (`STORAGE_KEY = 'jumping_frog_he_save_v1'`):** Stores total banked `coins`, current `level`, unlocked items, and equipped selections.
* **3 Store Tabs (`renderStore`):**
  1. **Frogs (`FROGS_CATALOG`):** Starter Gray Frog (`צפרדע אפורה` - free), Male Frog (`צפרדע זכר` - bow tie), Female Frog (`צפרדע נקבה` - blush & eyelashes), Prince Frog (`צפרדע נסיך` - circlet), and King Frog (`צפרדע מלך` - royal crown, cape & golden aura).
  2. **Dye Colors (`COLORS_CATALOG`):** Sketch Gray, Forest Green, Water Blue, Strawberry Red, Royal Purple, and Shiny Gold.
  3. **Accessories (`ACCESSORIES_CATALOG`):** None, Party Hat, Sunglasses, Pirate Hat, Hero Cape, and Royal Crown—rendered live on both the pond frog and the store preview canvas (`#store-preview-canvas`).

## 4. File Structure in Repository

* `index.html` — Complete game source code (downloaded from `jumping-frog.html` and renamed to `index.html` for GitHub Pages).
* `README.md` — Hebrew/English player & repository guide.
* `HANDOFF.md` — This technical context file for future development sessions.

## 5. Key Functions & Systems in `index.html`

* `PondAudio` — Web Audio API procedural sound effects (`playJump`, `playLand`, `playSplash`, `playCoin`, `playWin`, and mute toggle).
* `buildLevel(levelNum)` — Procedurally generates the starting pad, main path pads, off-path bonus `5`-coin pads, moving pads, and the `10`-coin goal flag pad.
* `drawFrogCharacter(targetCtx, x, y, sx, sy, scale)` — Renders the procedural frog character with squash-and-stretch animation, species traits, dye palette, and equipped accessories.
* `renderStore()` / `renderStorePreview()` — Handles store tab switching, purchases, equipping, and live canvas preview.

## 6. Roadmap / Ideas for Next Iterations

* [ ] Add optional pond hazards in higher levels (e.g., snapping turtles, dragonflies, or gusts of wind affecting trajectory).
* [ ] Add a level selector map so kids can replay earlier levels for 3-star completion.
* [ ] Add floating mid-air bonus flies/coins along high jump arcs.
* [ ] Add subtle background pond ambiance (croaks, gentle water ripples, or toggleable marimba melody).