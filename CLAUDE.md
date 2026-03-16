# Brawl Pong — Agent Instructions

## Project overview

A single-file browser game: **Brawl Stars-themed Pong** in `index.html`.
No build system, no dependencies, no bundler. Open the file directly in a browser to play.

## Architecture

Everything lives in `index.html`:
- **CSS** — inline `<style>` block; dark purple/cosmic theme, all layout via flexbox
- **JS** — inline `<script>` block; vanilla ES6+, Canvas 2D API for all rendering
- **Assets** — brawler portraits fetched at runtime from `cdn.brawlify.com`; canvas-drawn fallbacks exist for each brawler

### Key constants (top of `<script>`)
| Constant | Value | Purpose |
|---|---|---|
| `W`, `H` | canvas dimensions | arena size |
| `PADDLE_W` | 14 | paddle width px |
| `PADDLE_H` | 80 | standard paddle height |
| `COOP_PADDLE_H` | 58 | Co-Op paddle height (two paddles per side) |
| `BALL_SIZE` | 14 | ball diameter px |
| `WIN_SCORE` | 7 | points to win |

### State machine (`state.mode`)
- `'menu'` — main menu (sub-pages: `'mode'`, `'difficulty'`, `'brawler'`)
- `'playing'` — active game
- Game over is tracked via `state.over` / `state.winner`

### Game modes (`state.gameMode`)
- `'solo'` — 1 player vs bot
- `'duo'` — 2 players (P1: `W`/`S`, P2: `↑`/`↓`)
- `'coop'` — 2 players (P1+P3) vs bot; left side has two paddles stacked

### Players
- `p1` — left paddle, human (all modes)
- `p2` — right paddle, bot (solo/coop) or human (duo)
- `p3` — second left paddle, human (coop only)

### Rendering pipeline
`requestAnimationFrame` → `update()` → `draw()`

`draw()` calls in order: background → obstacles → power-ups → paddles → ball+trail → shields → HUD → avatar portraits → overlays (VS screen, pause, game over)

### Brawlers
Defined in `BRAWLERS` array. Each entry: `{ id, name, color, imgId, fallback }`.
- Portrait URL: `https://cdn.brawlify.com/brawlers/borderless/{imgId}.png`
- `fallback` — canvas draw function used if image fails to load

### Power-ups
Currently one type: **Shield** (`SHIELD_TYPE`). Spawns randomly mid-field. Collecting it activates a wall that bounces the ball back for 10 seconds.

### Obstacles
Two destructible crates near center (`CRATE_DEFS`). Each has `maxHp: 3`; hit effects and destruction particles are handled in `update()`.

## How to test changes

Open `index.html` directly in a browser — no server needed. Use browser DevTools console for debugging.

## Conventions

- All game text visible to the player is currently in **German** (`Spieler`, `Leicht`, `Mittel`, `Schwer`, etc.). Keep new UI text in German unless asked otherwise.
- Difficulty labels: `leicht` / `mittel` / `schwer` (lowercase keys in `DIFFICULTIES` object).
- Draw helpers (`drawRoundedRect`, `drawOutlinedText`, `drawGem`, `drawPaddle`, `spawnParticles`, etc.) are defined mid-file — reuse them before writing new ones.
- Keep everything in `index.html`. Do not split into separate files.
