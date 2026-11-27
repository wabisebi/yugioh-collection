# Card Collection

A personal page for Seb's second-hand Japanese Yu-Gi-Oh! cards (about 100, picked for the art). One self-contained `index.html`, no framework, no build step. Started 26 Nov 2026.

## Rules
- Never use em dashes, on the page or in replies.
- Keep replies short and informal. Seb is new to git: explain in plain words, one step at a time.
- Seb is a visual person: pictures over lists.
- No official logos. Card scans and pack pictures come from Yugipedia (credited in the footer).

## Files
- `index.html`: the whole site. All data is in the `<script>` at the bottom:
  - `GROUPS`: the three groups by what's printed under the art: `nocode` (no code), `code` (code without JP, including number codes like 301), `codejp` (code with JP).
  - `SETS`: one line per set (`code`, `name`, `jp`, `date` = Japanese release, `group`, optional `unsure` / `note` / `sample`). Groups sort by date by themselves.
  - `SET_IMG`: set code -> file in `photos/sets/` (the Japanese booster pack or deck box picture). Set tiles show this, never a card.
  - `CARDS`: one line per card (`set`, `code`, `name`, `jp`, `type`, `attr`, `lv`, `img`). Codeless cards have `code: ''` and are keyed by `name` (also in `FIRST`); their scans are `photos/<set>-<name-slug>.<ext>`, e.g. `photos/vol5-pale-beast.png`.
  - Seb sends codeless cards as romaji names (e.g. `PEIRUBIIZUTO` = ペイルビースト = Pale Beast). Use the card's codeless `jp_sets` lines to pick the set; if it was in more than one codeless set (often a Vol. plus the EX Starter Box, which look the same), file it under the first one and note it in "Still to check".
  - `FIRST`: card code -> [date, set name] of the card's FIRST Japanese release in any set (used by "First released" order on All cards). Seb's copy is often a later reprint.
- `photos/<code lowercase>.<ext>`: card scans, e.g. `photos/mr-11.jpg`, `photos/fotb-jp012.jpg`.
- `photos/sets/<set lowercase>.<ext>`: set pictures.
- The "Still to check" box near the bottom of `index.html` lists open questions (codes that match nothing etc.).

## Adding cards (Seb sends codes like `MR-11, FOTB-JP012`)
Never add doubles: before adding, check each card against `CARDS` by code and by card name. Skip any that's already there (even a different printing of the same card) and tell Seb which ones were skipped.
Use the browser pane on a yugipedia.com tab and call the MediaWiki API with `fetch('/api.php?...')` from there.
1. Find the card: full-text search for the code in quotes (`list=search&srwhat=text&srnamespace=*&srsearch="MR-11"`); the hit is `Set Card Lists:<Set> (OCG-JP)` plus the card page. Watch for typos: `F0TB` = `FOTB`, two codes stuck together. If a code matches nothing, try close codes, list the candidates for Seb and put it in "Still to check" instead of guessing.
2. Card data from the card page (`prop=revisions`, `redirects=1`): `ja_name` (strip `{{Ruby|漢字|かな}}` to 漢字), `types` / `card_type`, `attribute`, `level`.
3. First release: the card page's `jp_sets` block (lines `code; Set name; rarity`, codeless sets have an empty code). For each set read `jp_release_date` from the set page (try `<name> (set)` if missing), take the earliest. The movie Starter Box shows as "Starter Box (movie release)".
4. Card scan: `prop=images` on `Card Gallery:<card>`, pick the file with `-<SETPREFIX>-JP-` (e.g. `Peacock-MR-JP-C.jpg`; the rarity at the end must match the gallery line for that code), get the URL with `prop=imageinfo&iiprop=url`, download with `curl -sfL -A "Mozilla/5.0"` into `photos/`.
5. New set: add it to `SETS` (Japanese name and `jp_release_date` from the set page) and `SET_IMG`. The set page's `image` is sometimes the English pack; look for `<PREFIX>-BoosterJP` / `-DeckJP` in `prop=images` instead.
6. Add the `CARDS` line and the `FIRST` line. Check in the preview that every picture loads and the timeline labels don't overlap.

## Preview
`preview_start` with name `cards` (Python's built-in server on port 8010, from `.claude/launch.json`). Outside Claude, double-click `index.html`.

## Git and hosting
- Public GitHub repo `wabisebi/yugioh-collection` (made 26 Nov 2026, private at first, public the same day, Seb asked: he didn't want to set up Cloudflare for this), branch `main`. Routine: pull when Seb sits down, commit and push when done.
- Site: GitHub Pages from `main`, repo root, no build step. Every push goes live within a minute or two. Public: anyone with the link can see it (Seb is fine with that, even with the Yugipedia scans).
