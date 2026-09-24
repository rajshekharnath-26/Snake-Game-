# Happy Slither (Snake Game)

## Product goals (kid-friendly)
- A colorful, gentle, kid-friendly Snake game for ages ~5–12.
- Offline-first during gameplay: **no ads, no analytics, no tracking**, and **no network requests after the initial page load**.
- Accessible by default:
  - keyboard playable with visible focus outlines,
  - WCAG 2.1 AA contrast,
  - honors `prefers-reduced-motion`.

## Non-goals (v1.0)
- Multiplayer, global leaderboards.
- Accounts/logins and personal data collection.
- Ads, in-app purchases, analytics.

## Tech direction (architecture)
- TypeScript + Vite.
- Canvas 2D for gameplay rendering.
- Keep the core logic **deterministic and DOM-free**:
  - Put all state updates, movement, collisions, scoring, food spawning, progression rules in a pure `GameEngine`.
  - No direct DOM access in engine.
  - No timers/intervals inside the engine.
  - UI/input/rendering/audio/storage live in separate layers.
  - Randomness in the engine must be **seedable** (so unit tests are reproducible).

## How to run
```bash
npm install
npm run dev
npm test
npm run build
```

## Repo notes
- Core logic lives in `src/engine/*`.
- Progress/settings live in `src/storage/*`.
- Rendering/UI/input/audio live outside `src/engine/*`.

## Gameplay requirements (v1.0 target)
### Core loop
- Fixed-timestep simulation (tick-based logic).
- Rendering may use interpolation/smoothing between ticks, but the engine tick results stay deterministic.
- Input buffering: **up to 2** queued turns so quick key presses/swipes are never dropped.
- Movement rule: snake cannot reverse directly into itself.

### Grid
- Responsive square grid.
- Default: **15×15 on phones**, **20×20 on larger screens**.

### Food & bonus
- One fruit at a time; fruit spawns only on empty cells.
- Fruit types (visual variety): apple, strawberry, banana, grapes, cherry.
- Each fruit: **+1** point.
- Bonus item: occasional sparkling golden star for a **time-limited** bonus: **+5**.

### Scoring, progression, and end-of-run stars
- Score increases with fruit/bonus; maintain **personal best per mode**.
- At the end of each run, show a star rating (typically **1–3 stars**) based on score thresholds.
- Milestone celebrations at **score 10, 25, 50, 100**:
  - confetti + cheerful sound cue.

### Friendly failure
- Replace “GAME OVER” with encouraging language (e.g. “Nice try! Let’s go again!”) + one large **Play Again** button.

## Game modes (v1.0 target)
- **Cozy** (default): wrap-around walls; slow, gentle ramp-up; no time pressure.
- **Classic**: walls end the game.
- **Speedy**: walls end the game; faster ramp-up.

## Controls, pause, and input
- Keyboard: Arrow keys or `W A S D`.
- Pause/Resume: `Space` or `P`, plus a Pause button.
- Auto-pause when the tab loses focus.
- Touch: swipe anywhere on the board **or** use an on-screen D-pad.
- Mouse: clickable menus and buttons.
- Gamepad: nice-to-have (D-pad + A/Start).

## Accessibility & UX constraints
- Fully keyboard playable.
- Visible focus on all interactive elements.
- Touch targets at least **48×48 px**.
- Color is never the only signal:
  - fruit differs by shape as well as color.
- Reduced motion:
  - honor `prefers-reduced-motion` by reducing/disabling confetti, wobble, and other motion-heavy effects.
- Calm by default:
  - avoid flashing, screen shake, and harsh motion.
- Screen reader support:
  - semantic HTML for menus,
  - ARIA labels on icon-only buttons,
  - live region announcements for score changes.

## Visual style targets
- Bright, soft, rounded, kid-friendly visuals.
- Pastel-forward palette:
  - mint `#B8F2E6`
  - sky `#AEE1FF`
  - sunny yellow `#FFE66D`
  - coral `#FF8FA3`
  - lavender `#CDB4FF`
  - deep-ink text `#2D2A4A`
- Snake:
  - rounded segments,
  - friendly eyes on the head (blink),
  - gentle squash-and-stretch when eating.
- Fruit:
  - glossy cartoon look,
  - soft idle wobble.
- Board:
  - checkerboard of two soft tones per theme.
- Typography:
  - rounded display font (e.g. Fredoka) with OFL/SIL licensing.
  - **Self-host** fonts so gameplay makes no external requests.

## Audio
- SFX: **ON** by default.
- Music: **OFF** by default (toggle in UI).
- Audio rules:
  - never start audio until the user has interacted (click/tap/keypress),
  - respect device mute state.
- Sound cues:
  - eat, turn (soft), bonus star, milestone sounds, try-again.
  - milestone confetti + cheerful sound at score 10/25/50/100.

## Privacy & offline requirements
- No accounts/logins.
- No personal data collection.
- No ads, cookies, trackers, or third-party scripts.
- No network requests during gameplay (and ideally during the entire session after first load).
- Assets must be bundled:
  - do not fetch fonts/images/audio at runtime during gameplay.
- Progress/settings saved locally only via `src/storage/*`.
- If storage is blocked/unavailable, the game must fail gracefully.
- Keep external links (GitHub) behind a clearly labeled, parent-oriented label.
- Repository must include a clear `PRIVACY.md`.

## Screens (v1.0 target)
1. Start screen: animated logo + giant Play button, mode picker (3 large icon buttons), sound toggle, Skins button.
2. Game screen: score + best score, pause button, canvas board, optional on-screen D-pad for touch.
3. Pause overlay: Resume, Restart, Home.
4. Result screen: encouraging message, score/stars animation, new-best badge, **Play Again** and Home.
5. Skins/Themes screen: unlockable items; locked items show clear star cost.
6. Settings: sound on/off, music on/off, controls (swipe or D-pad), reduced-motion toggle, reset progress.
7. About: credits, license info, link to GitHub repo.

## Rewards & progression (v1.0 target)
- Stars unlock:
  - snake skins (e.g., Green Buddy, Sunny Yellow, Rainbow, Polka Dot, Space Snake),
  - themes (Garden, Ocean, Candy Land, Space).

## When adding new features
1. Keep engine deterministic + DOM-free (`GameEngine`).
2. Keep UI logic separate from state updates:
   - engine produces state/events,
   - renderer/UI consumes them.
3. Update/add unit tests for engine/storage logic changes.
4. Preserve offline/privacy constraints (no network during gameplay).
5. Preserve accessibility and reduced-motion behavior.

## Testing expectations
- Engine/storage logic: unit tests (Vitest).
- Optional smoke/e2e tests: Playwright for quick coverage of core flows.

## Milestones (v1.0 plan)
- **M1 — Engine**: pure engine + tests + three modes.
- **M2 — Look & Feel**: visuals/animations/audio, all screens, touch/keyboard controls.
- **M3 — Rewards & Polish**: stars/skins/themes, settings, accessibility, performance tuning, PWA/offline.
- **M4 — Repo & Release**: docs, CI checks, GitHub Pages demo, licenses/attributions, polished release process.
