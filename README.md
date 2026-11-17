# Tile Explorer

A colourful tile-matching puzzle game in the style of Tile Explorer / Triple Tile.
Pick free tiles from a stacked board, collect them in a 7-slot tray, and match
3 identical tiles to clear them. Clear the whole board before the tray fills up.

Single file, no dependencies, works on desktop and mobile browsers.

---

## Features

- 30 levels with rising difficulty
- 3 tile themes: **Phal** (fruit and food), **Janwar** (animals), **Fun** (space, games, objects), up to 30 different tiles
- Combo system with encouraging messages ("Wah!", "Zabardast!", "Kamaal!", "Mashallah!"...)
- Hint and Magnet helpers (paid with coins)
- Undo and Shuffle
- Star rating per level (up to 3 stars)
- Score, coins, unlocked levels and theme are saved in the browser
- Background music and sound effects generated live with the Web Audio API (no audio files)
- Light and dark mode, reduced-motion support, mobile safe areas

---

## How to play

1. Tap a tile that is not covered by another tile. Covered tiles look darker and cannot be picked.
2. The tile flies into the tray at the bottom.
3. When 3 identical tiles are in the tray, they burst and disappear.
4. Clear every tile on the board to win the level.
5. If the tray gets full (7 tiles) without a match, the level is lost and restarts.

### Controls

| Button | What it does | Cost |
|---|---|---|
| Undo | Takes back your last tile (not possible after a match) | Free (affects stars) |
| Shuffle | Re-mixes the remaining tiles on the board | Free (affects stars) |
| Levels | Opens the level map | Free |
| Sound icon | Turns music and sound effects on or off | Free |
| Hint | Makes a good tile pulse yellow for 4 seconds | 5 coins |
| Magnet | Automatically picks the tiles needed to complete a match | 10 coins |
| Theme | Cycles between Phal, Janwar and Fun | Free |

### Scoring and rewards

- Each match: **30 x combo** points and **1 coin**
- Combo continues if you match again within **5 seconds**
- Level clear: **+100 points and +5 coins per star**
- Stars: 3 stars with no Undo/Shuffle, 2 stars with 1 to 2 uses, 1 star with 3 or more

### Level difficulty

| Setting | Formula |
|---|---|
| Different tile types | `min(4 + ceil(level / 2), number of icons)` |
| Sets of 3 tiles | `min(4 + floor(level * 1.2), 22)` |
| Stack layers | `3 + min(floor(level / 4), 4)` |
| Music tempo | slightly faster each level (`max(0.17, 0.26 - level * 0.003)` seconds per note) |

---

## Design

### Colour palette

The app uses CSS variables on `:root`. Dark mode values apply automatically through `prefers-color-scheme`.

| Variable | Light | Dark | Used for |
|---|---|---|---|
| `--bg` | `#e8f1ec` | `#13221f` | Page background (soft animated gradient) |
| `--ink` | `#1d3a34` | `#e6f2ec` | Text |
| `--tile` | `#fffdf6` | `#f6f1df` | Popup card, default tile |
| `--edge` | `#c9b98a` | `#a8996b` | Tile edge fallback |
| `--tray` | `#2f6b5e` | `#2f6b5e` | Tray background |
| `--slot` | `#255449` | `#1b3d35` | Empty tray slots |
| `--btn` | `#e0813a` | `#e0813a` | Buttons |
| `--btn-ink` | `#ffffff` | `#ffffff` | Button text |

Accent colours:

| Colour | Hex | Used for |
|---|---|---|
| Gold | `#ffd23f` | Praise text, hint glow, tray flash on match |
| Burnt orange | `#b5471b` | Praise text shadow |
| Alert red | `#e15554` | Tray "almost full" warning glow |
| Slate grey | `#7d8a86` | Locked level buttons |
| Confetti | `#ffd23f` `#e0813a` `#3bb273` `#4d9de0` `#e15554` | Level-complete confetti |

### Tile colours

Every tile type gets its own hue, so matching tiles are easy to spot:

```
hue          = (tileTypeIndex * 47) mod 360
face         = linear-gradient(150deg, hsl(hue 100% 98%), hsl(hue 85% 86%))
border       = hsl(hue 50% 62%)
3D edge      = hsl(hue 45% 48%)
```

A white glossy highlight (an oval `::after` layer) sits on the top part of each tile.

### Typography

- Font stack: `"Trebuchet MS", "Segoe UI", system-ui, sans-serif` (no web fonts to download)
- Title: 18px, bold
- Stats (level, score, coins, combo): 14px, bold
- Buttons: inherit the font, bold; 13px for the helper buttons
- Praise popup: `clamp(34px, 11vw, 60px)`, weight 900, gold with a burnt-orange shadow and glow
- Tile icons: emoji at 58% of the tile width

### Layout and shapes

- Maximum content width: 480px, centred
- Board grid: 6 columns by 7 rows with half-step offsets and stacked layers (each layer shifts slightly up and right to look 3D)
- Tile size: scales to the screen, aspect ratio 1 : 1.15, corner radius 12px
- Tray: 7 equal slots, 16px radius, 8px padding
- Buttons: 12px radius, orange fill, visible focus outline for keyboard users

### Animations

| Animation | Where |
|---|---|
| Tiles drop in with a bounce and spin | Level start and Shuffle |
| Tile flies from board to tray | Every pick |
| Slot pop-in | Tile lands in the tray |
| Star burst | 3 tiles match |
| Gold glow on tray | Match |
| Red pulsing glow on tray | Tray has 6 or 7 tiles |
| Pulsing yellow tile | Hint |
| Hover lift and press squash | Free tiles |
| Praise text zoom and float | Matches and level clear |
| Falling confetti | Level clear |
| Slowly shifting background gradient | Always |

All motion is switched off when the device has "reduce motion" enabled.

### Sound

Generated with the Web Audio API:

- Music: a cheerful 16-step loop, triangle-wave melody over a sine-wave bass
- Effects: short click on pick, rising chime on match, fanfare on win, falling tone on loss
- Audio starts after the first tap (browsers require a user gesture)

---

## Run it

1. Download `tile-explorer.html`
2. Open it in any modern browser (Chrome, Edge, Safari, Firefox)

No install, build step or internet connection is needed.

## Project structure

```
tile-explorer.html   Everything: HTML, CSS and JavaScript in one file
README.md            This file
```

Inside the HTML file:

- `<style>`: variables, tile and tray styling, animations
- `<script>`: game state, level generation, picking and matching, audio, themes, save/load

## Customise

- **Add or change tiles:** edit the `ICONS` array (Phal) or the `TH` array (themes)
- **More levels:** raise `TOTAL` (the difficulty formulas keep scaling, with caps)
- **Colours:** change the `:root` variables at the top of the stylesheet
- **Hint and Magnet prices:** change the numbers passed to `spend()` and `pay()` in `doHint` and `doMagnet`
- **Tray size:** the tray is 7 slots (CSS grid with 7 columns, plus the checks for `7` in the script)

## Saved data

Progress is stored in the browser under the key `te_save` in `localStorage`:
highest unlocked level, stars per level, coins, score and theme.
If the browser blocks storage, the game still works but does not remember progress.

## Known limitations

- Levels are generated randomly, so a board can occasionally be harder than expected (use Hint, Magnet or Shuffle)
- Tiles are emoji, so their look depends on the device's emoji font
- No account or online leaderboard; progress stays on one device and browser
