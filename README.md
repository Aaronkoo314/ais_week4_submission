# ais_week4_submission

A side-by-side comparison harness for two PantryPilot deployments, used to A/B the old
build against the new one in a single view.

## Contents

| File | What it is |
| --- | --- |
| `pantrypilot-ab.html` | Standalone comparison UI. No build step, no dependencies. |
| `pantrypilot-heuristic-comparison.md` | Written evaluation of the two builds against Nielsen's ten heuristics. |

## Running it

Open `pantrypilot-ab.html` in a browser. That's the whole setup — it is a single
self-contained file (fonts come from Google Fonts; everything else is inline).

## The two deployments

| Side | Deployment |
| --- | --- |
| Old | `pantrypilotclaudecopy-nm5plvk3h-aaronkoo2026-7097.vercel.app` |
| New | `pantrypilot-44ebi1vno-aaronkoo2026-7097.vercel.app` |

## What the UI does

- **Two live panes.** Each deployment renders in its own frame, tagged `OLD` (ochre) and
  `NEW` (green), so the accent colour alone tells you which build you're looking at.
- **Shared path field.** Type a route and press Enter to point both panes at the same path.
- **Viewport presets.** `Fill`, `1280`, `768`, `390` — each fixed width is scaled down to fit
  its pane, so both sides are measured at exactly the same viewport.
- **Swap sides.** Flips left/right to defeat position bias when judging a visual change.
- **Per-pane status chip.** `Loading` / `Rendered` / `No response`, plus `Sign-in required`
  for a deployment that can't be embedded.

## Known limitation: the old deployment can't be embedded

Verified 23 Sep 2026:

- The **new** deployment answers `200` with no `X-Frame-Options` and no CSP `frame-ancestors`,
  so it embeds normally.
- The **old** deployment answers `302` and redirects to Vercel's SSO login, which sends
  `X-Frame-Options: DENY`. This is Vercel Deployment Protection, not a bug in the page —
  no browser will frame it while that redirect is in place.

The old pane explains this inline and offers **Retry**, **Open in new tab**, and
**Show pane anyway**. To get it rendering side by side:

1. Vercel dashboard -> the `pantrypilot` project -> **Settings** -> **Deployment Protection**.
2. Turn off **Vercel Authentication**, or add the deployment to **Protection Bypass**.
3. Reload the page and press **Retry**.

A Vercel shareable link (`?_vercel_share=...`) also works — swap it in for the origin in the
`DEPLOYS` array near the top of the `<script>` block.

## Changing the URLs

Both deployments are declared in one place, at the top of the inline script:

```js
var DEPLOYS = [
  { role:"old", label:"Old", origin:"https://...", gated:true  },
  { role:"new", label:"New", origin:"https://...", gated:false }
];
```

`gated: true` makes a pane open with the sign-in notice instead of a blank frame.

## Note on in-frame navigation

Clicking around inside a pane navigates that frame, but the panes are cross-origin — the page
cannot read a frame's current URL back, so the path field does not follow along. Set the path
from the toolbar when you want both sides on the same route.
