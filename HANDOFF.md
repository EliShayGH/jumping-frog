# Jumping Frog (`הצפרדע הקופצת`) — Project Handoff & Context

## 1. Project Overview

* **Concept:** A single-file, zero-dependency 2D HTML5 Canvas web game inspired by a hand-drawn sketch of a frog jumping across lily pads over a blue watercolor pond to reach a red-and-white goal flag.
* **Target Audience:** Kids (Hebrew-speaking interface, `dir="rtl"` layout, playful hand-drawn watercolor notebook aesthetic).
* **Repository & Hosting:** Hosted on GitHub Pages at `https://github.com/EliShayGH/jumping-frog` (Live URL: `https://elishaygh.github.io/jumping-frog/`).
* **Architecture:** 100% self-contained inside a single `index.html` file (HTML, CSS, Canvas 2D procedural watercolor & ink rendering, parabolic trajectory physics, and Web Audio API sound synthesis).

## 2. Core Gameplay Mechanics

* **Touch slingshot (primary control, Phase 1):** `pointerdown` on the canvas grabs the frog (within `SLING_GRAB_RADIUS`) or anchors anywhere on the pond. Pull direction sets the angle, pull length sets `aimPower` (up to `slingMaxPull()`); pulls shorter than `SLING_DEADZONE` cancel on release. `applySlingAim()` recalculates every frame so drifting pads don't skew aim; `drawSlingOverlay()` draws the rubber band / start ring. Works with mouse too.
* **Optional buttons:** the arrow/jump buttons are hidden unless `progress.showButtons` (toggled by the 🎮 HUD button, saved).
* **Aiming & Trajectory Arrow (`drawAimingArrow`):**
  * **Up (`↑` / `W` / On-screen `חזק +`):** Increases jump power (`aimPower`), lengthening the colored trajectory arrow and extending the parabolic preview dots.
  * **Down (`↓` / `S` / On-screen `חלש -`):** Decreases jump power, shortening the arrow for closer lily pads.
  * **Right (`→` / `D` / On-screen `ימינה`):** Rotates the arrow clockwise (lower, flatter arc down to `14°`).
  * **Left (`←` / `A` / On-screen `שמאלה`):** Rotates the arrow counter-clockwise (higher, steeper arc up to `78°`).
  * **Launch (`Space` / `Enter` / On-screen `🦘 קפוץ!`):** Launches the frog with velocity `(cos(rad) * aimPower, -sin(rad) * aimPower)` under `GRAVITY = 980`.
* **Lily Pads, Hazards & Camera:**
  * Landing safely on a green lily pad restores jumping and updates `frog.safePadIndex`.
  * Missing a lily pad triggers a water splash (`triggerSplash()`) and returns the frog to the last safe lily pad without losing progress.
  * Pads store `ny` (relative height in the water) so `resize()` keeps them in place on rotation.
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
* `fonts/` — Bundled Rubik (Hebrew/Latin/Arabic) and Assistant woff2 files + their OFL licenses; no network fonts.
* `package.json`, `capacitor.config.json`, `android/` — Capacitor 8 Android app (see §7).
* `www/` — generated, git-ignored copy of `index.html` + `fonts/` + `capacitor.js` (`npm run build:www`). Edit the root `index.html`, never `www/`.
* `NEW_GAME_PLAYBOOK.md` lives with Sketch Bird; this project follows it. **Phase 1 is done.** **Phase 2 in progress** (see §9).
* `sketches/` — git-ignored design sketches shown to the owner.

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
## 7. Android app (Phase 1)

* **App ID (temporary):** `com.quantumpairstudios.frogpond`, app name "Jumping Frog". The final game name isn't picked yet; when it is, change `appId`/`appName` in `capacitor.config.json`, `namespace`/`applicationId` in `android/app/build.gradle`, `android/app/src/main/res/values/strings.xml`, and move `MainActivity.java` to the new package folder. Do this **before** the first Play Console upload (the ID can't change after that).
* **Why `www/` is generated:** the root `index.html` is also the live GitHub Pages site, so it stays at the root. `build:www` copies it into `www/` and adds `<script src="capacitor.js">` only there.
* **Native setup (copied from Sketch Bird):** landscape only (`sensorLandscape`), full screen with hidden system bars (`MainActivity.java`), `AD_ID` permission removed (Families policy), Gradle 9.1.0 (needed for Android Studio's Java 25 on this Mac).
* **Back button** (`handleBack()` in `index.html`, app only): store → closes it; aiming → back to the start screen (level kept); mid-jump/splash → ignored; level complete → start screen with the next level; start screen → "לצאת מהמשחק?" confirm (`#exit-overlay`).
* **Tuning:** `MAX_POWER` lowered 920 → 700 → 620 (owner: 700 still a bit strong) so a full pull is just under two pads on a phone (the phone screen is ~717×326 CSS px). The water/hills edge strokes now only trace the shoreline/hill line (they used to outline the screen edges with a white/dark line).
* **Build & install:** `npm run sync`, then in `android/`: `JAVA_HOME="/Applications/Android Studio.app/Contents/jbr/Contents/Home" ./gradlew assembleDebug`, then `adb -s <device> install -r app/build/outputs/apk/debug/app-debug.apk`.
* **Icon & splash (Phase 2, owner picked "icon 2"):** gray frog on a lily pad on blue water, drawn with the game's own frog code. Adaptive icon = `mipmap-*/ic_launcher_background.png` (water) + `ic_launcher_foreground.png` (frog + pad inside the safe circle); legacy/round PNGs; splash = paper color with the frog on its pad (no text, works in every language). Play Store 512 icon: `store-assets/icon-512.png`.
* **Not done yet:** Xiaomi install.

## 8. Owner decisions (Nov 2026)

* **Audience:** same as Sketch Bird: kids and families (friendly copy, Families-policy-safe ads).
* **Languages:** all 5 like Sketch Bird: Hebrew, English, Arabic, Spanish, French (right-to-left for Hebrew and Arabic). Every new string goes in all 5; Settings gets a language picker.

## 9. Phase 2 progress

Approved Phase 2 list: (1) home screen, (2) settings, (3) pause, (4) first-play tutorial, (5) vibration, (6) tidy the top bar for phones, (7) level-complete screen, (8) opening animation, (9) app icon + splash.

**Done:**
* **5 languages:** `LANGUAGES` + `I18N` (he/en/ar/es/fr) after the catalogs. HTML text uses `data-i18n="key"` / `data-i18n-title="key"`; `applyLanguage()` fills them and sets `dir`. Dynamic text uses `t('key', ...args)`; catalog names via `frogName/frogDesc/colorName/accName/accDesc`. Language is saved in `progress.language`; the first launch uses the phone language (fallback English).
* **Home screen** (owner picked layout C with A's title): the pond is the menu. Big lily pad with the frog = Play, side pads = Shop / Settings. Pads + frog are drawn on the canvas by `drawHomeScene()` using `homeLayout()`; transparent `.home-pad` buttons sit on top (`layoutHomeButtons()`). `#start-overlay` is now this home layer, so existing show/hide code still works. HUD/touch buttons are hidden at home (`.at-home`). The old instructions card is gone; a one-time hint toast (`firstHint`, `progress.seenHint`) shows on first play until the tutorial (item 4) exists.
* **Settings** (`#settings-overlay`, state `SETTINGS`): language picker + sound (saved in `progress.muted`). Vibration and privacy link to be added.
* **Icon + splash:** see §7.

**Next:** pause menu, tutorial, vibration, top-bar tidy, level-complete screen, opening animation.
