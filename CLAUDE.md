# Toddler Games — Project Guidelines

## Architecture
- Single HTML file (`index.html`) with inline CSS + JS, no external dependencies
- Canvas 2D game loop with `requestAnimationFrame`, dt-based updates
- 16+ mini-games sharing a common engine (particles, audio, buttons, characters)

## Adding New Games — Checklist
1. `startXGame()`, `updateXButtons()`, `updateXGame(dt)`, `drawXGame()` functions
2. Add menu entry to `games` array in `showMenu()` with `{emoji, label, bg, fn}`
3. Add to game loop if/else chain: `else if(currentMode==='xxx'){updateXGame(dt);drawXGame()}`
4. Add canvas tap handler if needed (both `click` and `touchstart` listeners)
5. Add drag handlers if needed (`mousemove`, `touchmove`, `mouseup`, `touchend`)
6. Reset `wobbleAngle=0;wobbleVel=0;` in start function if game uses wobble physics
7. **Do NOT add a Menu button** — the fixed home button (top-left) handles navigation
8. **Do NOT set `instruction.textContent`** if the game draws its own text on canvas — causes duplicate display
9. Use `btn-again` class ONLY for restart/replay buttons in done states (auto-positioned top-right with 1.5s delay)
10. Use `btn-go`, `btn-done`, `btn-food`, `btn-menu` etc. for gameplay buttons — NOT `btn-again`

## Display Rules
- `instruction.textContent` shows in a fixed bar at top-center (z-index 60)
- If a game draws text on the canvas, set `instruction.textContent=''` to avoid duplication
- The fixed home button is at top-left, restart button auto-positions top-right

## Sizing & Layout
- Use `Math.min(W,H)` for proportional elements — NOT `W` or `H` alone
- Check portrait mode: `H > W * 1.5` — adjust ground/track/scene positions
- Dig game ground: `digGroundY = H > W*1.5 ? H*0.42 : H*0.55`
- Menu uses 3-column grid with scrollable overflow

## Audio
- All volumes must be ≤ 0.15 (headphone safe)
- Square/sawtooth waves ≤ 0.12 (harshest waveforms)
- Use `playTone(freq, dur, type, vol, delay)` — delay parameter schedules on AudioContext timeline (required for iOS)
- **Do NOT use `setTimeout` with `playTone`** — iOS blocks audio in setTimeout callbacks
- Use `sayWord(word)` for speech — automatically selects English voice on non-English devices
- Use `if(navigator.vibrate)navigator.vibrate(ms)` for haptic feedback on impactful moments

## Browser Compatibility (Legacy iPad Safari)
- **CSS**: Always provide `100vh` fallback before `100dvh` (dvh unsupported on iOS < 15.4)
- **CSS**: `env(safe-area-inset-*)` needs fallback values
- **CSS**: `overscroll-behavior` not supported on older Safari — add `-webkit-overflow-scrolling` fallback
- **JS**: `.flat()` requires iOS 12+
- **JS**: `navigator.vibrate` does not exist on iOS — always guard with `if(navigator.vibrate)`
- **JS**: Initialization wrapped in try-catch to show error instead of blank brown screen

## Performance Caps
- Particles capped at 200 (`spawnParticles` returns early if over)
- Confetti capped at 100 (`spawnConfetti` returns early if over)
- Pizza toppings capped at 40
- Dump stream: 4 rock particles per frame max
- All particle arrays cleaned up on game switch via `showMenu()`

## Dig Game Hole Physics
- Sand: stacks from bottom, each layer = `(hh-12)/10` height
- Water: fills on top of sand like a pool (semi-transparent, proportional to amount)
- Rocks: scattered individual stones that land on current surface (sand top or hole bottom)
- Rocks don't displace water; sand pushes water up
- Draw order: sand → water → rocks (rocks visible through water)
- After 10 layers: hole transforms (sandcastle/pond/mountain based on dominant material)

## Touch Handling
- `touch-action: none` on body and canvas
- Global `touchmove` preventDefault blocks all browser gestures
- `gesturestart`/`gesturechange` blocked for iOS pinch
- Paint safe zone: top 40px and bottom 100px excluded from splats
