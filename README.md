# Learning

Two self-contained flashcard pages, no build step, nothing sent anywhere:

| | |
|---|---|
| **Bike Anatomy Trainer** — `index.html` | Name the highlighted part of a road bike. 👉 https://parallax-parking.github.io/learning/ |
| **Human Action Review** — `human-action.html` | End-of-chapter questions for Mises's *Human Action*, chapters I–VII. 👉 https://parallax-parking.github.io/learning/human-action.html |

Both share the same look, the same mastery scoring and the same scheduler; they differ in how you
answer. The bike has one right word, so you type it and the page marks it. A philosophy chapter has
no single right sentence, so you write your answer, reveal the model answer, and grade yourself.

---

# Bike Anatomy Trainer

An Anki-style flashcard trainer for the **30 named parts of a road bike**, built as a single
self-contained HTML page. A part of the bike lights up, you type its name, and it turns green
if you're right and red if you're wrong. Anything you miss goes back in the pile and comes
around again until it sticks.

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
The bike doubles as a heat map of how you're actually doing:

| Dot | Meaning |
|---|---|
| ⚪️ grey | Not yet tested |
| 🟡 amber | Getting there — answered right, but not yet twice running |
| 🟢 green | Solid — named unaided the last 2 times in a row |
| 🔴 red | Shaky — missed within the last 3 attempts |

### Mastery is recent form, not a permanent record

Each part keeps a rolling window of its last 6 attempts (`1` = named unaided, `0` = missed or
revealed). A hint-assisted answer records **nothing** — it neither proves you know the part nor
proves you don't.

The point is that red **heals**. Two unaided answers in a row and a part goes green, however many
times you fumbled it last week. A lifetime miss counter would brand a part red forever, which
tells you where you *were* rather than where you *are* — and would keep feeding you parts you
mastered a fortnight ago.

### Decks

Drill the whole bike, or narrow to **Frame**, **Drivetrain**, **Braking**, **Cockpit**, **Wheel**,
or **My trouble spots**, which rebuilds itself from whatever is currently red — the deck
selector shows the count, and it shrinks as parts go green.

### Scheduling

Missed cards return after a few other cards — not immediately, so you can't parrot the answer
back, and not at the end, so you actually get another go. New cards and lapsed cards are
interleaved, which guarantees you meet every part in the deck rather than looping the first
handful.

Attempt history and your best score are kept in `localStorage`, per device. Nothing is sent
anywhere. **Reset all saved progress** in the footer wipes it.

---

## Enabling GitHub Pages

Already enabled: **Deploy from a branch**, `claude/interactive-bike-parts-quiz-hv38l9` / `(root)`.
`.nojekyll` is included so GitHub serves the files as-is. Each push to that branch rebuilds
the site automatically.

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

Mastery thresholds live in three constants near the top of the script — `WINDOW` (how many
attempts are remembered), `SOLID_RUN` (consecutive hits needed to go green) and `RECENT` (how far
back a miss still counts as shaky).

### Using a different diagram

The quiz engine doesn't know anything about bicycles. To retrain it on another diagram, replace
the `<svg id="bike">` contents and rewrite `PARTS` with markers in the new coordinate space —
the scheduler, matcher, stats and both modes carry over unchanged.

---

# Human Action Review

End-of-chapter review for Ludwig von Mises's *Human Action*. One card per question, 128 cards
across chapters I–VII (Acting Man · The Epistemological Problems of the Sciences of Human Action ·
Economics and the Revolt Against Reason · A First Analysis of the Category of Action · Time ·
Uncertainty · Action Within the World), including 18 **key terms** to define. Every numbered
section and every unnumbered sub-heading on the book's contents page (*On Happiness*, *I and We*,
*Vegetative Man* …) has its own questions.

## How it works

A question is like an essay prompt, not a label on a diagram, so the page can't mark you. Instead
it runs the way a good study partner would:

| | |
|---|---|
| **Prompt** | One question, tagged with its chapter and section. Write your answer in the box, or just think it through. |
| **Reveal** | <kbd>⌘</kbd>/<kbd>Ctrl</kbd> + <kbd>Enter</kbd>, or the button. Shows what you wrote, the **model answer**, a checklist of the points a full answer should hit, and an analogy. |
| **Grade** | Tick the points you covered — the matching grade lights up as a suggestion — then pick <kbd>1</kbd> Missed it, <kbd>2</kbd> Partly, or <kbd>3</kbd> Got it. |
| **Skip** | Parks it for later without logging a miss. |

The three grades do different things to the schedule:

- **Got it** retires the card for this run and records a hit.
- **Partly** brings it back after a few other cards and records *nothing* — the equivalent of the
  bike trainer's hint-assisted answer. It neither proves you know it nor proves you don't.
- **Missed it** brings it back soon and records a miss.

### Think of it like…

Every answer comes with an analogy, because a formal definition is a photograph of a concept and an
analogy is a handle on it. "Rationality refers to means, not ends" is the photograph; "a satnav can
pick the fastest road but has no opinion on whether you should be going to the coast" is the handle.
The checklist carries the photograph; the orange box carries the handle.

### Browse mode

Every question laid out the way the book's contents page is: chapter → numbered section →
sub-heading, with the same grey / amber / green / red mastery dot as the bike and a per-section tally
of what is solid and what is shaky. Open any question to read the answer, points and analogy without
being quizzed — useful right after finishing a section, before the first run. Each chapter and section
heading has a **Review →** button that starts a run on just those cards.

### Decks

The deck menu mirrors the same structure: all chapters, **Key terms only**, **My trouble spots** (which
rebuilds itself from whatever is currently shaky), and then, per chapter, the whole chapter or any one
numbered section — so you can drill §2 *The Prerequisites of Human Action* on its own the evening you
read it.

Mastery, scheduling and storage are the same rolling-window scheme as the bike trainer (see above),
kept under a separate `localStorage` key, so resetting one page leaves the other alone.

## Adding chapters

Everything lives in the `CARDS` array near the top of the `<script>` in `human-action.html`. One
entry per card:

```js
{id:"c1-happiness", ch:1, s:2, sub:"On Happiness", kind:"q",
 q:"In what sense does all action aim at <em>happiness</em> …?",
 a:"Every action tries to exchange a less satisfactory state …",
 pts:["Action always substitutes a more satisfactory state …","…"],
 ana:"Saying ‘everyone acts to be happier’ is like saying …"},
```

| field | what it does |
|---|---|
| `ch` | Chapter number |
| `s` | Numbered section within the chapter, as on the contents page |
| `sub` | Optional: the unnumbered sub-heading the question belongs to (`"On Happiness"`). Omit for questions on the section proper. |
| `kind` | `"q"` for a review question, `"term"` for a definition (goes in the Key terms deck) |
| `q` | The question. `<em>` highlights a word in orange. |
| `a` | The model answer |
| `pts` | The checklist — the things a complete answer should contain |
| `ana` | The analogy shown under "Think of it like…" |

### House style for cards

- **Plain English.** Write the answer the way you would explain it aloud to a friend who has not read
  the book. No textbook vocabulary (*praxeological*, *a priori*, *heterogeneous*, *serviceableness*)
  unless the card is *about* that term, in which case the first sentence explains it in everyday words.
- **Short.** Answers run 50–90 words, never more than about 110. Term cards can be shorter.
- **One question per card.** If it asks two things, split it or drop one.
- **Checklist points restate the answer.** Three points, occasionally four, each under about 14 words,
  each a plain restatement of a claim the answer explicitly makes, in the order the answer makes them.
  Nothing appears in a point that does not appear in the answer.
- **Keep Mises's claims.** Simplify the language; do not add claims, examples or opinions.

### Roadmap

Chapters I–VII are in the deck. The rest of the book, from the 4th revised edition (FEE, 1996), for
when the next chapters are read — section lists are included where they will be needed next:

- [x] **Introduction** — not in the deck; the review starts at Part One
- [x] **I. Acting Man**
- [x] **II. The Epistemological Problems of the Sciences of Human Action**
- [x] **III. Economics and the Revolt Against Reason**
- [x] **IV. A First Analysis of the Category of Action** — 1 Ends and Means · 2 The Scale of Value · 3 The Scale of Needs · 4 Action as an Exchange
- [x] **V. Time** — 1 Time as a Praxeological Factor · 2 Past, Present, and Future · 3 The Economization of Time · 4 The Temporal Relation Between Actions
- [x] **VI. Uncertainty** — 1 Uncertainty and Acting · 2 The Meaning of Probability · 3 Class Probability · 4 Case Probability · 5 Numerical Evaluation of Case Probability · 6 Betting, Gambling, and Playing Games · 7 Praxeological Prediction
- [x] **VII. Action Within the World** — 1 The Law of Marginal Utility · 2 The Law of Returns · 3 Human Labor as a Means · 4 Production
- [ ] **Part Two: Action Within the Framework of Society** — VIII Human Society · IX The Role of Ideas · X Exchange Within Society
- [ ] **Part Three: Economic Calculation** — XI Valuation Without Calculation · XII The Sphere of Economic Calculation · XIII Monetary Calculation as a Tool of Action
- [ ] **Part Four: Catallactics or Economics of the Market Society** — XIV The Scope and Method of Catallactics · XV The Market · XVI Prices · XVII Indirect Exchange · XVIII Action in the Passing of Time · XIX Interest · XX Interest, Credit Expansion, and the Trade Cycle · XXI Work and Wages · XXII The Nonhuman Original Factors of Production · XXIII The Data of the Market · XXIV Harmony and Conflict of Interests
- [ ] **Part Five: Social Cooperation Without a Market** — XXV The Imaginary Construction of a Socialist Society · XXVI The Impossibility of Economic Calculation Under Socialism
- [ ] **Part Six: The Hampered Market Economy** — XXVII The Government and the Market · XXVIII Interference by Taxation · XXIX Restriction of Production · XXX Interference with the Structure of Prices · XXXI Currency and Credit Manipulation · XXXII Confiscation and Redistribution · XXXIII Syndicalism and Corporativism · XXXIV The Economics of War · XXXV The Welfare Principle Versus the Market Principle · XXXVI The Crisis of Interventionism
- [ ] **Part Seven: The Place of Economics in Society** — XXXVII The Nondescript Character of Economics · XXXVIII The Place of Economics in Learning · XXXIX Economics and the Essential Problems of Human Existence

The printed contents page also lists unnumbered sub-headings inside some sections (*On Happiness*,
*I and We*, *Vegetative Man* …). Those are what the `sub` field holds; copy them from the book when
adding a chapter, since the online contents list leaves them out.

To add a chapter, add it to `CHAPTERS` — numeral, full title, short title, and its `secs` list copied
from the book's contents page (`{n, t, subs:[…]}`) — and add its number to the `[1,2,3]` arrays in
`buildDeckSelect` and `renderList`. The deck menu and Browse layout are generated from `CHAPTERS`, so a
sub-heading with no cards yet shows up in Browse marked *(no cards yet)* until you write some.
