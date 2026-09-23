# PantryPilot — before and after, against Nielsen's ten heuristics

**Aaron Koo** · 23 September 2026 · MGMT 6110 Week 5

What changed: the setup screen, which carried the whole ingredient list plus four
more questions on one page, is now six pages — the five existing ingredient
categories in order, then the four questions that are not about ingredients.

The ingredient data, the five categories and the second-level groups are
untouched. Only the layout moved.

All ten heuristics are covered below, including the four this round did not
change and the two where the product is still weak. Measurements are from the
built app at a 375-pixel viewport.

| | Before | After |
| --- | --- | --- |
| Setup screen | one page, 1906px, growing every time a category was opened | six pages, 937–1522px each |
| Longest page | unbounded — opening Meat & Seafood added 25 items | 1522px (the four questions) |
| Horizontal overflow | none | none |
| Irreversible actions | one (Clear all) | none |

---

## 1 · Visibility of System Status — **changed**

**Before.** A 1906px page with no indication of how much was below the fold, and
none of how much was left to do. The only progress signal was the live meal
count on the Find Meals button.

**After.** "1 of 6" and a six-segment bar sit above every page. The current
segment is filled, completed ones are tinted, and the live meal count moved onto
the Next button so it is still visible at every step rather than only at the end.

What was already good and was kept: the count itself, the match percentage on
each card, and the service status row that says whether the live nutrition
lookup is working.

---

## 2 · Match Between the System and the Real World — **changed**

**Before.** The page header under "How many people?" read *"We scale every recipe
to this number."* That was half true. The number seeds the serving stepper on the
meal detail screen, but nothing on a meal **card** responds to it: a user who sets
six people still sees cards reading "Serves 2".

**After.** *"We scale the recipe when you open it."* — which is what the code
does.

Unchanged and still right: the time options speak in evenings rather than in
numbers ("Barely any time", "A normal weeknight", "I can take my time"), and the
step headings are the category names a cook would use rather than invented
labels.

---

## 3 · User Control and Freedom — **changed, and this is the biggest repair**

**Before.** **Clear all was irreversible.** Fourteen ingredients ticked, one
mis-tap, everything gone, with no confirmation and no undo. It was the only
irreversible action in the product.

**After.**

- Clear all is undoable. The tray reports *"Cleared 3 ingredients. Undo"* and the
  undo restores the exact list. A confirmation dialog was rejected: it charges
  every user a tap to protect the few who misfire, while an undo charges only the
  people who actually misfired.
- Back on every page except the first.
- Every progress segment is a button, so no one has to walk forward through pages
  they do not care about.
- Every ingredient in the tray has its own × .
- Searching does not move you off the page you are on.

---

## 4 · Consistency and Standards — **partly changed; one known defect left**

**After.** A chip behaves identically wherever it appears — in the most-used row,
inside an opened group, in search results, and as a tag in the tray. Selecting
and unselecting is the same gesture in all four places.

**Still wrong, and not touched this round.** The results screen's filter
disclosure prints the weight bands as raw ids:

> Time, cuisine and weight — `30 min · 1 cuisine · light + medium`

Everywhere else in the product they are `Light` and `Medium`. The cause is
`FilterBar.jsx:27`, which joins the ids rather than their labels. It is a
one-line fix on a screen this round deliberately left alone.

---

## 5 · Error Prevention — **changed**

**Before.** Nothing prevented the confusing states; they were simply not
reachable because there was only one page.

**After, three rules that stop a wrong state existing rather than warning about it:**

- **A category of eight or fewer shows everything, with no "See all" link.**
  Dairy & Eggs has seven ingredients. Promoting six and hiding the seventh behind
  a link would cost a tap to reveal almost nothing, which teaches the user that
  the link is not worth pressing.
- **Skip is a separate button from Next**, doing the same thing. Next on its own
  leaves a user wondering whether moving on means "I have none of these" or "I
  have not finished with this page".
- **Selections survive everything** — moving forward, jumping via a segment,
  searching, and returning from the results screen.

One regression was introduced and caught while measuring: `.secondary-button` is
`width: 100%` everywhere else in the app, which inside the new flex button bar
pushed the primary button 67px off the right edge. Overriding the flex basis was
not enough; the width had to be overridden too.

---

## 6 · Recognition Rather than Recall — **changed**

**Before.** Picks were echoed in an "In your kitchen" block at the top of the
single card, which scrolled away as you worked down the page.

**After.** The "Your ingredients" tray is on **every** page, carrying the count
and every pick as a removable tag. You never have to remember what you ticked two
pages ago.

Kept from before: group headers still carry item and selected counts, so a closed
group reads as an index rather than an empty box.

---

## 7 · Flexibility and Efficiency of Use — **changed**

Three routes to the same ingredient, for three kinds of user:

| Route | For |
| --- | --- |
| The six most-used chips | somebody stocking a normal kitchen |
| "See all 26 …" | somebody with something unusual |
| Search, from any page, across all 93 | somebody who knows exactly what they are holding |

Search is the one thing that ignores the page structure entirely, because a
person at an open fridge should not have to work out which of six pages holds
fish sauce.

**How "most used" is decided, and why it is not an opinion.** It is derived from
the 47 recipes — the six ingredients each category's own recipes call for most
often. An authored "common" flag would have been roughly forty judgements nobody
could check, and it would drift the moment a recipe changed. This is the same
argument the project already makes for calories, the weight band and the
vegetarian flag.

| Category | Most-used row |
| --- | --- |
| Vegetables & Aromatics | Garlic (39), Spring Onion (23), Red Chilli (21), Coriander (17), Ginger (16), Lime (11) |
| Pantry & Flavour | Olive Oil (27), Light Soy Sauce (22), White Pepper (21), Palm Sugar (17), Sesame Oil (13), Shaoxing Wine (12) |
| Grains & Noodles | Jasmine Rice (6), Bread (3), Egg Noodles (2), Glass Noodles (2), Pasta (2), Rice Noodles (2) |
| Dairy & Eggs | none — seven items, all shown |
| **Meat & Seafood** | **none — see below** |

**Meat & Seafood is the interesting exception.** Every recipe carries exactly one
main protein, so the counts there top out at three with a four-way tie, and
ranking by frequency would put Chickpeas and Red Lentils above Chicken Breast.
Counting cannot separate proteins, so that page shows its six second-level groups
instead — which separate them properly. The rule in the code is general: if the
first-ranked ingredient does not beat the sixth by at least four uses, no row is
shown. Meat & Seafood is currently the only category that trips it.

Pantry & Flavour gets both a most-used row **and** its groups, because olive oil
and light soy sauce are each in about a quarter of the recipes, and making
somebody open "Chinese" to reach soy sauce is a tap for nothing.

---

## 8 · Aesthetic and Minimalist Design — **the reason for the change**

**Before.** 1906px with every category collapsed, and unbounded in practice: one
tap on Meat & Seafood added twenty-five items to the same page.

**After.** Six pages, each between 937 and 1522px.

| Page | Height | Screens |
| --- | --- | --- |
| 1 · Meat & Seafood | 1056px | 1.30 |
| 2 · Vegetables & Aromatics | 1009px | 1.24 |
| 3 · Grains & Noodles | 1009px | 1.24 |
| 4 · Dairy & Eggs | 937px | 1.15 |
| 5 · Pantry & Flavour | 1111px | 1.37 |
| 6 · A few last questions | 1522px | 1.87 |

**A caution about this heuristic, stated honestly.** The six pages add up to more
pixels than the one page did. That is not a contradiction — no user meets the
sum, they meet one page at a time, and a page you can finish is worth more than a
shorter page you cannot see the end of. But it does mean the change traded
*scrolling* for *tapping*, and the defence of that trade is the jumpable progress
bar: a user who only wants vegetables taps one segment rather than five Next
buttons.

The heuristic's stronger reading — that every extra element dilutes the relevant
ones — is **not** yet applied to the meal detail screen, which is 4747px and 5.8
screens. That is the next piece of work.

---

## 9 · Help Users Recognize, Diagnose and Recover from Errors — **partly changed**

**New this round.** The undo described under heuristic 3, which turns a
destructive mistake into a recoverable one.

**Already good, and unchanged.** The results screen's empty state is the
strongest thing in the product on this heuristic. It does not say "no results";
it names each filter that is actually binding and offers to undo that one:

> Nothing fits those settings.
> `Show meals with missing items too` `Allow any weight` `Allow any cuisine` `Allow 60+ minutes` `Edit my kitchen`

**Still improvable, not done.** Those buttons do not say how much each would
bring back. `Allow any cuisine (+16)` would let a user choose in one read instead
of trying them one at a time. The counts already exist in `App.jsx`.

---

## 10 · Help and Documentation — **not changed, and the weakest of the ten**

The product has no help of any kind: no "?", no key, no explanation of any label.
Mostly it does not need one, because the interface explains itself in words as it
goes.

**One thing genuinely needs it.** Every meal card and detail screen carries a tag
row like:

> 🥢 Chinese · Stir-fry · **Medium** · **Easy**

`Medium` is the calorie band. `Easy` is the difficulty. They are two unrelated
facts, both rendered as a single adjective with no unit, sitting next to each
other — "Medium Easy" reads as one thing. And `Easy` is **defined nowhere in the
product**: it takes two values across 47 meals, filters nothing, sorts nothing,
and no source publishes it.

The honest options are to define it on screen or to remove it. Neither was done
this round.

---

## What this round did not touch

- **The meal detail screen**, 4747px and 5.8 screens, with no index. The section
  heights are Ingredients 1140px, Nutrition 1219px, Steps 972px — 70% of the page
  in three blocks.
- **The `light + medium` casing** on the results screen (heuristic 4).
- **Counts on the empty-state buttons** (heuristic 9).
- **The undefined difficulty label** (heuristic 10).
- **A live defect**: the sourced nutrition panel returns "Lunchmeat, chicken
  breast, sliced" for Chicken Breast, with all three macro values blank, and the
  function still reports it as `ok`. The product was correct on 11 September; USDA
  published a new record in April that now outranks the raw-chicken one. Nothing
  in this repository changed — the provider did.

---

## One regression caught while writing this

Splitting the setup into steps replaced the old page header, and with it the line
**"Cook what you already have"** — the only sentence on the first screen that said
what the product was for. Classmates asked what the product did had all quoted
that line and nothing else.

It is restored, on page one only, where a stranger arrives. Pages two to six are
read by somebody who already knows, so it does not take their space.

Worth recording because of how it happened: the heuristic being applied was
minimalism, the line looked like decoration, and removing it cost the product its
answer to heuristic 1 on the only screen where that question gets asked. Making
something shorter and making it clearer are not the same move.
