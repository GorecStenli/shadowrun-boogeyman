# Boogeyman — Shadowrun 3rd Edition Character Sheet

An interactive, self-contained HTML character sheet for **Boogeyman** (real name Oleg; street names *Babayaga*, *Babay*) — a gun-fu assassin built for Shadowrun 3rd Edition (SR3).

## Open it

Online: `https://gorecstenli.github.io/shadowrun-boogeyman/` (once GitHub Pages is enabled — see *Online version* below).

Offline: open [`index.html`](index.html) in any modern browser (desktop or mobile). No build step — everything is embedded in the one file; without internet it shows the copy last stored in that browser.

## What's inside the sheet

- **Core** — attributes (natural / augmented), vitals, pools (Combat 9, Karma 7), movement & lift, transport (vehicle stats — Eurocar Westwind 2000), edges/flaws, contacts, background, and a character portrait.
- **Skills** — active, knowledge and language skills with specializations.
- **Combat** — condition monitor, weapons, armor and accessories. Weapons/armor/accessories are driven live from the Gear tab (only items in the "On hand / worn" group show here).
- **Gear** — interactive inventory with nuyen tracking. Items are split into two groups — **On hand / worn** and **Backpack (in trunk)** — and sorted by type with icons (weapon, armor, optics, tool, ammo, grenade, medical…). Drag items between groups or use the arrow button. `+` adds an item (deducting its cost) via a search over a Cannon Companion equipment database; `−` removes it after a Yes/No confirm (no refund).
- **Ware** — cyberware (with grade/essence) and bioware (with body index), each with a short effect note.

## Project files

```
index.html            The complete character sheet (portrait embedded as a data URI)
data/                 Created by the app on first save: character.json (sheet data), key.json (encrypted token)
.nojekyll             Tells GitHub Pages to serve the files as-is
assets/
  portrait.jpg        Source character portrait (embedded into index.html)
```

The portrait is kept here as the original for future editing. `index.html` already contains its own embedded copy, so the file works on its own without it.

## Notes

- Built for SR3 rules (Essence/Body-Index budgets, Combat Pool, smartlink, etc.). Some equipment costs are approximate — verify against Cannon Companion / Man & Machine with your GM.
- Tested on Chrome, Firefox and Edge, desktop and mobile.

## Development notes

- The whole app is a single self-contained file, `index.html` — HTML, CSS and JS inline, with the portrait embedded as a `data:` URI. (The background music was removed before going public — it was a copyrighted soundtrack track.) It also exists as a Claude artifact (live preview).
- Tabs: Core / Skills / Combat / Gear / Ware — they sit inside the sticky header, so they stay on screen while scrolling.
- Working state lives in the browser's `localStorage`, under two keys:
  - `lividum_sheet_v3` — every editable field (attributes, pools, vitals, skills, cyber/bioware…), keyed by its `data-k` id.
  - `boogeyman_gear_v3` — the inventory: `{ cash, items: [ { n: name, c: cost, g: group } ] }`, where `g` is `"w"` (on hand / worn) or `"s"` (backpack / stowed).
- The sheet and gear scripts are `window.__initSheet` / `window.__initGear`; the sync script at the bottom of `index.html` loads the online copy into `localStorage` first, then starts them. Their `save()` calls `window.__markDirty()` to flag unsaved changes.
- Combat, Armor and Accessories are derived live from the gear items in the **worn** group; gear rows are typed (weapon / armor / optic / tool / ammo / grenade / medical…), icon-coded and sorted by type.

## Online version (GitHub Pages)

The sheet is published with GitHub Pages and keeps its data in this repo, so it reads the same on every device. Anyone with the link can view it; only the owner can save.

- **Data:** `data/character.json` — `{ v, saved, sheet, gear }` (`sheet` = the `lividum_sheet_v3` object, `gear` = the `boogeyman_gear_v3` object). Every save is a commit, so the git history is the change log and backup.
- **Loading:** read through the GitHub API (always fresh). If the API is unavailable (e.g. its 60 requests/hour limit for anonymous visitors), the copy published by Pages is used instead.
- **Saving:** the **Save** button writes `data/character.json` through the GitHub API. That needs a GitHub fine-grained token (this repo only, *Contents: read & write*). The token is stored encrypted (PBKDF2-SHA256 + AES-GCM) with the owner's password in `data/key.json`; the password itself is stored nowhere.
- **Password:** the correct password unlocks saving on that device for **8 hours** (also across reloads); after that, Save asks for it again. The password box has a *show* button to check what was typed.
- **Visitors' edits:** anyone can tap and change values, but without the password the changes stay in their own browser only ("unsaved changes (only on this device)"), with a *revert to online* link. They are dropped automatically as soon as a newer version is saved online.
- **Two devices:** if the online copy changed since the page was loaded, Save asks before overwriting it.

### Setup (once)

1. Make the repo public and enable Pages: *Settings → Pages → Deploy from a branch → `main` / root*. The site is then at `https://gorecstenli.github.io/shadowrun-boogeyman/`.
2. Create a fine-grained token: GitHub *Settings → Developer settings → Fine-grained tokens* — repository access: only `shadowrun-boogeyman`; permission *Contents: Read and write*.
3. Open the site, press **Save → token setup** (or open the URL with `#setup`), paste the token and choose a long password. This uploads `data/key.json` and unlocks saving for 8 hours.
4. Press **Save** on the device/browser that has the latest version of the sheet — the first save publishes that device's data.

When the token expires, create a new one and run *token setup* again (the password can stay the same or change).

Note: `index.html` must stay self-contained apart from the `data/` files; keep `OWNER`, `REPO` and `BRANCH` in the sync script in step with the repo.

## Continuing work in a new chat

To pick up the project without dragging a long conversation along (cheaper on tokens — a fresh chat starts with a clean context):

1. Start a **new chat connected to this repository**.
2. Point Claude at this repo (`GorecStenli/shadowrun-boogeyman`) and state the goal.
3. Claude reads `index.html` and this README, then continues from there.

A short opening message is enough, e.g.:

> This repo is my Shadowrun 3rd-ed character sheet (see README.md). I want to rebuild it as an online version — read `index.html` and let's start.

When a change is done, ask Claude to commit and push it so the repo stays the source of truth.

