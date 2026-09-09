# Bike Anatomy Trainer

An Anki-style flashcard trainer for the **30 named parts of a road bike**, built as a single
self-contained HTML page. A part of the bike lights up, you type its name, and it turns green
if you're right and red if you're wrong. Anything you miss goes back in the pile and comes
around again until it sticks.

**Live page:** enable GitHub Pages (see below), then visit
`https://parallax-parking.github.io/learning/`

---

## How it works

The bike is drawn as an **SVG**, not a photograph. That matters: because every tube, cog and
bearing is a real shape in the drawing, the app can light up the *actual part* — the whole
down tube, the whole chain line — rather than just dropping a dot on top of a picture. It also
means the page is one file with no images to load, and it stays sharp on any screen.

### Quiz mode

| | |
|---|---|
| **Prompt** | One part pulses orange with a `?` callout |
| **Answer** | Type the name, press <kbd>Enter</kbd> |
| **Right** | Turns green, tells you what the part does, auto-advances |
| **Wrong** | Turns red, names the part, and — if you named a *different* real part — tells you which one you actually described |
| **Hint** | Shows the category plus the first letter of each word (`Drivetrain · C···· ····`) |
| **Show answer** | Reveals it and counts as a miss |
| **Skip** | Parks it for later without logging a miss |

Answers are matched forgivingly: **aliases** (`seat` → Saddle, `tire` → Tyre, `rear mech` →
Rear derailleur, `shifter` → Brake/shift lever), plurals, leading articles, and small typos
(`top tubbe` passes) all count. Genuinely different parts do not — `seat post` will never be
accepted for `seat tube`.

### Explore mode

Tap anywhere on the bike, or pick from the grouped list, to see any part named and highlighted.
Parts you've missed before are marked red in the list with a miss count, so your weak spots are
visible before you start.

### Decks

Drill the whole bike, or narrow to **Frame**, **Drivetrain**, **Braking**, **Cockpit**, **Wheel**,
or **My trouble spots** (auto-built from everything you've ever got wrong on this device).

### Scheduling

Missed cards return after a few other cards — not immediately, so you can't parrot the answer
back, and not at the end, so you actually get another go. New cards and lapsed cards are
interleaved, which guarantees you meet every part in the deck rather than looping the first
handful.

Progress, miss counts and your best score are kept in `localStorage`, per device. Nothing is
sent anywhere. **Reset all saved progress** in the footer wipes it.

---

## Enabling GitHub Pages

1. Repo → **Settings** → **Pages**
2. **Source:** Deploy from a branch
3. **Branch:** pick the branch holding `index.html`, folder `/ (root)`
4. Save, wait a minute, and load `https://parallax-parking.github.io/learning/`

`.nojekyll` is included so GitHub serves the files as-is.

---

## Editing the deck

Everything lives in the `PARTS` array near the top of the `<script>` in `index.html`.
One entry per card:

```js
{id:"downtube", n:"1b", name:"Down tube", cat:"Frame",
 alias:["down tube","downtube"],
 note:"The biggest tube on the bike — head tube down to the bottom bracket.",
 x:564, y:346, hi:'<path d="M451 449 L678 243"/>'},
```

| field | what it does |
|---|---|
| `name` | The answer, and what's shown when revealed |
| `alias` | Everything else that should be accepted |
| `note` | The one-line explanation shown after you answer |
| `cat` | Groups it in Explore, and drives the deck filter and hint |
| `x`, `y` | Where the marker sits, in the SVG's `viewBox` coordinates (`70 40 860 580`) |
| `hi` | SVG shape to light up — reuse the same geometry as the drawing itself |

To make a card highlight as a thin stroke rather than a fat one, add its `id` to the `THIN` map.

### Using a different diagram

The quiz engine doesn't know anything about bicycles. To retrain it on another diagram, replace
the `<svg id="bike">` contents and rewrite `PARTS` with markers in the new coordinate space —
the scheduler, matcher, stats and both modes carry over unchanged.
