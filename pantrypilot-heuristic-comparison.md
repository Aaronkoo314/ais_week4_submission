# PantryPilot — before and after, against Nielsen's ten heuristics

**Aaron Koo** · 23 September 2026 · MGMT 6110 Week 5

One change was made: the setup screen, which carried the whole ingredient list
plus four other questions on a single page, is now six pages — the five existing
ingredient categories in order, then the four questions that are not about
ingredients.

No ingredient, category or second-level group was altered. No screen other than
setup was touched.

**Honest summary before the detail: this was one layout change.** Three heuristics
gained something real, one gained something small, four were not changed at all,
and one is where the change was aimed. Several of the things that look like
improvements below are the new design paying for a cost it introduced itself, and
those are marked as such rather than counted as gains.

| Heuristic | This round |
| --- | --- |
| 1 · Visibility of System Status | one real addition |
| 2 · Match With the Real World | one sentence corrected |
| 3 · User Control and Freedom | one real addition; three items pay for the wizard |
| 4 · Consistency and Standards | **no change** |
| 5 · Error Prevention | **net zero** — new rules, for new problems |
| 6 · Recognition Rather than Recall | small |
| 7 · Flexibility and Efficiency | one real addition |
| 8 · Aesthetic and Minimalist | the target of the change |
| 9 · Recognize, Diagnose, Recover | **no change** beyond the undo in 3 |
| 10 · Help and Documentation | **no change** — still the weakest |

Measurements are from the built app at a 375-pixel viewport.

---

## 1 · Visibility of System Status — one real addition

**Added:** "1 of 6" and a six-segment progress bar above every page. Before, a
1906px page gave no indication of how much was below the fold or how much was
left to do.

**Not added, already there:** the live meal count (it moved from the Find Meals
button to the Next button — same information, different place), the match
percentage on each card, the group headers' item and selected counts, and the
service status row. None of these was improved.

---

## 2 · Match With the Real World — one sentence corrected

The hint under "How many people?" read *"We scale every recipe to this number."*
That was half false: the number seeds the serving stepper on the detail screen,
but nothing on a meal **card** responds to it — set six people and the cards still
read "Serves 2".

It now reads *"We scale the recipe when you open it."*

That is the whole of the change under this heuristic. The time options
("Barely any time", "A normal weeknight") and the category names were already in
the user's language and were not touched.

---

## 3 · User Control and Freedom — one real addition

**The real one: Clear all is now undoable.** It was the only irreversible action
in the product — fourteen ingredients ticked, one mis-tap, everything gone, no
confirmation and no undo. The tray now reports *"Cleared 3 ingredients. Undo"* and
the undo restores the exact list.

A confirmation dialog was considered and rejected: it charges every user a tap to
protect the few who misfire, where an undo charges only the people who did.

**Three things that look like gains and are not.** Back, Skip, and the jumpable
progress segments all exist because the wizard created the problem they solve.
The old single page needed no way to go back through steps, because it had none.
These are the new design paying its own bill, not an improvement on the old one.

**One thing that is roughly neutral.** Each ingredient in the tray now has an × .
Before, tapping a chip in the "In your kitchen" block removed it. Same capability,
slightly clearer affordance.

---

## 4 · Consistency and Standards — no change

Chips behave identically in the most-used row, inside an opened group, in search
results and in the tray — but they did before, too. Nothing about consistency was
improved this round.

**The known defect is still there and was not touched.** The results screen's
filter disclosure prints the weight bands as raw ids:

> Time, cuisine and weight — `30 min · 1 cuisine · light + medium`

Everywhere else they are `Light` and `Medium`. `FilterBar.jsx:27` joins the ids
instead of the labels. One line, on a screen this round left alone.

---

## 5 · Error Prevention — net zero

Three rules were added, and all three prevent problems that the wizard itself
introduced:

- **A category of eight or fewer shows everything, with no "See all".** Dairy &
  Eggs has seven. This matters only because "See all" is new; there was no such
  link before.
- **Skip is a separate button from Next.** This resolves an ambiguity that only
  exists once there are steps to skip.
- **Selections survive moving, jumping, searching and returning from results.**
  There was nothing to survive on a single page.

A regression was also introduced and then fixed: `.secondary-button` is
`width: 100%` everywhere else in the app, which inside the new flex button bar
pushed the primary button 67px off the right edge at 375px. Caught by measuring,
not by looking.

**So the honest accounting is that error prevention neither improved nor got
worse.** New failure modes were created and then handled.

---

## 6 · Recognition Rather than Recall — small

The "Your ingredients" tray now appears on each of the six pages. Before, the
same information sat once at the top of the single page and scrolled away as you
worked down it.

Measured: the tray is reachable without scrolling on pages 1 to 5 (it sits
513–685px down a 812px viewport) and **not** on page 6, where it starts at
1099px. So "always visible" would overstate it; "repeated per page, and above the
fold on five of six" is accurate.

---

## 7 · Flexibility and Efficiency of Use — one real addition

**The real one: the most-used shortcut.** Each large category now leads with the
six ingredients its own recipes call for most, with "See all" behind it. Before,
reaching garlic meant opening a category and scanning twenty-six items.

**Not new:** search already covered all 93 ingredients from the single setup
page. It still does, from any of the six pages. Jumping via a progress segment
is, again, the wizard paying its own bill.

**How "most used" is decided.** Derived from the 47 recipes, not authored. An
authored "common" flag would have been roughly forty judgements nobody could
check, and it would drift the moment a recipe changed — the same argument this
project already makes for calories and the vegetarian flag.

| Category | Most-used row |
| --- | --- |
| Vegetables & Aromatics | Garlic (39), Spring Onion (23), Red Chilli (21), Coriander (17), Ginger (16), Lime (11) |
| Pantry & Flavour | Olive Oil (27), Light Soy Sauce (22), White Pepper (21), Palm Sugar (17), Sesame Oil (13), Shaoxing Wine (12) |
| Grains & Noodles | Jasmine Rice (6), Bread (3), Egg Noodles (2), Glass Noodles (2), Pasta (2), Rice Noodles (2) |
| Dairy & Eggs | none — seven items, all shown |
| Meat & Seafood | none — counting cannot separate them |

**Meat & Seafood is worth a line.** Every recipe carries exactly one main protein,
so the counts top out at three with a four-way tie, and ranking by frequency
would put Chickpeas and Red Lentils above Chicken Breast. That page shows its six
existing second-level groups instead. The rule in the code is general — no row
unless the first-ranked ingredient beats the sixth by at least four uses — and
Meat & Seafood is currently the only category that trips it.

---

## 8 · Aesthetic and Minimalist Design — the target of the change

**Before:** 1906px with every category collapsed, and unbounded in practice —
one tap on Meat & Seafood added twenty-five items to the same page.

**After:** six pages of 937 to 1522px.

| Page | Height | Screens |
| --- | --- | --- |
| 1 · Meat & Seafood | 1056px | 1.30 |
| 2 · Vegetables & Aromatics | 1009px | 1.24 |
| 3 · Grains & Noodles | 1009px | 1.24 |
| 4 · Dairy & Eggs | 937px | 1.15 |
| 5 · Pantry & Flavour | 1111px | 1.37 |
| 6 · A few last questions | 1522px | 1.87 |

**Two things this is not.**

It is not a reduction. **No content was removed** — the same 93 ingredients and
the same four questions are still there, repartitioned. The six pages add up to
more pixels than the one page did. The defence is that no user meets the sum;
they meet one page at a time, and a page you can see the end of is worth more
than a shorter one you cannot. But the change traded scrolling for tapping, and
that is a trade rather than a win.

It is also not the stronger reading of this heuristic — that every extra element
dilutes the relevant ones. Under that reading the meal detail screen is the real
target, at 4747px and 5.8 screens, and it was not touched.

---

## 9 · Recognize, Diagnose and Recover from Errors — no change beyond the undo

The undo under heuristic 3 is the only thing this round added, and it is counted
there rather than twice.

The strongest thing in the product on this heuristic was already there and was
not altered: the results screen's empty state names each filter that is actually
binding and offers to undo that one, rather than saying "no results".

> Nothing fits those settings.
> `Show meals with missing items too` `Allow any weight` `Allow any cuisine` `Allow 60+ minutes` `Edit my kitchen`

It could still be better — those buttons do not say how much each would bring
back, and `Allow any cuisine (+16)` would let a user choose in one read. The
counts already exist in `App.jsx`. Not done.

---

## 10 · Help and Documentation — no change

Nothing was added and nothing needed removing. The product still has no help of
any kind.

Mostly it does not need any, because the interface explains itself in words as it
goes. **One thing does.** Every meal card and detail screen carries:

> 🥢 Chinese · Stir-fry · **Medium** · **Easy**

`Medium` is the calorie band, `Easy` is the difficulty. Two unrelated facts, both
a single adjective with no unit, adjacent — "Medium Easy" reads as one thing. And
`Easy` is defined nowhere: two values across 47 meals, filtering nothing, sorting
nothing, published by no source.

Define it on screen or remove it. Neither was done.

---

## Still outstanding

- **Meal detail screen** — 4747px, 5.8 screens, no index. Ingredients 1140px,
  Nutrition 1219px, Steps 972px: 70% of the page in three blocks.
- **`light + medium`** casing, `FilterBar.jsx:27` (heuristic 4).
- **Counts on the empty-state buttons** (heuristic 9).
- **The undefined difficulty label** (heuristic 10).
- **A live defect:** the sourced nutrition panel returns "Lunchmeat, chicken
  breast, sliced" for Chicken Breast with all three macro values blank, and the
  function still reports `ok`. The product was correct on 11 September; USDA
  published a record in April that now outranks the raw-chicken one. Nothing here
  changed — the provider did.

---

## One regression, caught while writing this up

Splitting the setup into steps replaced the old page header, and with it
**"Cook what you already have"** — the only sentence on the first screen that said
what the product was for. Classmates asked what the product did had all quoted
that line and nothing else.

Restored, on page one only.

It is worth recording how it happened. The heuristic being applied was
minimalism; the line looked like decoration; removing it cost the product its
answer to heuristic 1 on the one screen where that question actually gets asked.
Making something shorter and making it clearer are not the same move, and this
round confused them once.
