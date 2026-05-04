---
title: Work Log
description: Append-only audit trail. After each turn that creates, edits, or restructures content in the knowledge base, append one dated entry here (one per turn, not per file). Silent edits break the audit trail.
---
# Work Log

Append-only audit trail. **Append a dated entry after any turn that creates, edits, or restructures content in the knowledge base** — one entry per turn, not per file. Silent edits break the chain that makes knowledge-base changes auditable.

What to log:

- `ingest` runs (new external sources captured)
- `research` / `consolidate` runs (provisional or canonical articles produced)
- Direct `write_document` / `edit_document` / renames / deletions outside the three workflow tools
- Folder restructures (`ok seed`, manual reorganization)
- `.open-knowledge/config.yml` changes

**Reference docs as markdown links, not bare paths.** Every doc you touched should appear as `[path/to/doc](./path/to/doc.md)`so the log shows up in`get\_backlinks` for those docs. A bare path string (`Files touched: foo/bar.md\`) does not register in the doc graph — the audit trail compounds only when the log is a real linker.

<!-- Example entry shape:

## YYYY-MM-DD — <short title>

- <what was done>
- Files touched: `[path/to/doc-a](./path/to/doc-a.md)`, `[path/to/doc-b](./path/to/doc-b.md)`
- Sources ingested: `[source-slug](./external-sources/source-slug.md)`
- Open follow-ups: <topic-1>, <topic-2>

\-->

## 2026-04-30 — Manhattan music history wiki build

- Built a four-section cross-linked wiki covering Manhattan music history from the 1970s to present: 1 hub doc, 8 genres, 13 venues, 15 artists, 7 neighborhoods (44 articles total).
- Added `genres/`, `venues/`, `artists/`, `neighborhoods/` folder defaults to `.open-knowledge/config.yml` so per-folder titles, descriptions, and tags merge automatically at read time.
- Drafting parallelized via four sub-agents (one per section); all writes routed through `write_document` so the CRDT received agent attribution.
- Sourcing approach: drew on widely-documented New York music history (canonical books, NYT, Wikipedia, Pitchfork). External URLs are listed in each article's "Further reading" footer rather than ingested — this is a casual wiki, not a strictly-grounded research artifact. Future passes could `ingest` key sources (e.g., Lizzy Goodman's *Meet Me in the Bathroom*, Tim Lawrence's *Love Saves the Day*) for the closed-loop chain.
- Files touched: [HOME](./HOME.md), [genres/punk-rock](./genres/punk-rock/overview.md), [genres/no-wave](./genres/no-wave/overview.md), [genres/disco](./genres/disco/overview.md), [genres/hip-hop](./genres/hip-hop/overview.md), [genres/new-wave](./genres/new-wave/overview.md), [genres/post-punk-revival](./genres/post-punk-revival/overview.md), [genres/jazz](./genres/jazz/overview.md), [genres/electronic-dance](./genres/electronic-dance/overview.md), [venues/cbgb](./neighborhoods/lower-east-side/venues/cbgb.md), [venues/maxs-kansas-city](./neighborhoods/greenwich-village/venues/maxs-kansas-city.md), [venues/studio-54](./neighborhoods/midtown-times-square/venues/studio-54.md), [venues/paradise-garage](./neighborhoods/soho/venues/paradise-garage.md), [venues/mudd-club](./neighborhoods/soho/venues/mudd-club.md), [venues/danceteria](./neighborhoods/chelsea-meatpacking/venues/danceteria.md), [venues/knitting-factory](./neighborhoods/soho/venues/knitting-factory.md), [venues/bowery-ballroom](./neighborhoods/lower-east-side/venues/bowery-ballroom.md), [venues/mercury-lounge](./neighborhoods/lower-east-side/venues/mercury-lounge.md), [venues/apollo-theater](./neighborhoods/harlem/venues/apollo-theater.md), [venues/blue-note](./neighborhoods/greenwich-village/venues/blue-note.md), [venues/village-vanguard](./neighborhoods/greenwich-village/venues/village-vanguard.md), [venues/electric-lady-studios](./neighborhoods/greenwich-village/venues/electric-lady-studios.md), [artists/ramones](./genres/punk-rock/artists/ramones.md), [artists/blondie](./genres/punk-rock/artists/blondie.md), [artists/talking-heads](./genres/punk-rock/artists/talking-heads.md), [artists/patti-smith](./genres/punk-rock/artists/patti-smith.md), [artists/television](./genres/punk-rock/artists/television.md), [artists/sonic-youth](./genres/no-wave/artists/sonic-youth.md), [artists/beastie-boys](./genres/hip-hop/artists/beastie-boys.md), [artists/madonna](./genres/electronic-dance/artists/madonna.md), [artists/strokes](./genres/post-punk-revival/artists/strokes.md), [artists/lcd-soundsystem](./genres/post-punk-revival/artists/lcd-soundsystem.md), [artists/yeah-yeah-yeahs](./genres/post-punk-revival/artists/yeah-yeah-yeahs.md), [artists/interpol](./genres/post-punk-revival/artists/interpol.md), [artists/asap-mob](./genres/hip-hop/artists/asap-mob.md), [artists/vampire-weekend](./genres/post-punk-revival/artists/vampire-weekend.md), [artists/new-york-dolls](./genres/punk-rock/artists/new-york-dolls.md), [neighborhoods/lower-east-side](./neighborhoods/lower-east-side/overview.md), [neighborhoods/east-village](./neighborhoods/east-village/overview.md), [neighborhoods/greenwich-village](./neighborhoods/greenwich-village/overview.md), [neighborhoods/soho](./neighborhoods/soho/overview.md), [neighborhoods/harlem](./neighborhoods/harlem/overview.md), [neighborhoods/chelsea-meatpacking](./neighborhoods/chelsea-meatpacking/overview.md), [neighborhoods/midtown-times-square](./neighborhoods/midtown-times-square/overview.md).
- Sources ingested: none (closed-loop ingestion deferred — see sourcing note above).
- Open follow-ups: ingest canonical sources to convert "Further reading" URLs into local citations; consider expansion into Bronx/Brooklyn/Queens (hip-hop's true geography); add articles for TV on the Radio, Run-DMC, Wu-Tang business presence in Manhattan, Limelight, Mercer Arts Center, Cotton Club, Minton's, Nuyorican Poets Cafe; verified all internal cross-links via `get_dead_links` (clean for new docs; only pre-existing template placeholders in log.md remain unresolved).

## 2026-04-30 — Restructure: per-genre folders with artists subfolder

- Restructured the wiki: each genre is now its own folder containing `overview.md` (the genre article) plus an `artists/` subfolder for the bands most identified with it.
- Genre→artist mapping:
  - **punk-rock**: ramones, blondie, talking-heads, patti-smith, television, new-york-dolls
  - **no-wave**: sonic-youth
  - **hip-hop**: beastie-boys, asap-mob
  - **electronic-dance**: madonna
  - **post-punk-revival**: strokes, lcd-soundsystem, yeah-yeah-yeahs, interpol, vampire-weekend
  - **disco / new-wave / jazz**: overview only (no Manhattan artists in this wiki's primary-genre slot)
- Implementation: 23 `rename_document` calls in total — 8 to move genre files into folders, 15 to move artists into genre subfolders. Each rename atomically rewrote inbound markdown links across the wiki, including HOME.
- First attempt to use `rename_document` produced corrupted sibling references and phantom files (`venues/television.md`, `genres/television.md`); rolled back, restored Television to `artists/television.md`, and the user applied a server-side fix. Second attempt completed cleanly.
- CRDT-ghost cleanup: 7 stale doc records (left over from earlier sessions / first failed rename) kept resurrecting `.md` files in `artists/`, `genres/disco.md`, `genres/electronic-dance.md` after every Bash `rm`. Renamed each into `_phantoms/` to quarantine them out of the main structure. They will linger in the CRDT until manually purged server-side.
- `.open-knowledge/config.yml`: dropped the top-level `artists/**` rule, added `genres/*/artists/**` rule for nested artists, and added `_phantoms/**` rule labelling those orphan records.
- Files touched: every doc in the wiki — see updated paths under [genres/punk-rock/overview](./genres/punk-rock/overview.md), [genres/no-wave/overview](./genres/no-wave/overview.md), [genres/disco/overview](./genres/disco/overview.md), [genres/hip-hop/overview](./genres/hip-hop/overview.md), [genres/new-wave/overview](./genres/new-wave/overview.md), [genres/post-punk-revival/overview](./genres/post-punk-revival/overview.md), [genres/jazz/overview](./genres/jazz/overview.md), [genres/electronic-dance/overview](./genres/electronic-dance/overview.md), and the artist files under each genre's `artists/` subfolder.
- Open follow-ups: server-side purge of the `_phantoms/` CRDT records once the OK admin tool supports doc deletion; consider whether Blondie + Talking Heads should also link from `genres/new-wave/artists/` (currently they live only under punk-rock since that's their formation home).

## 2026-04-30 — Phantom CRDT records purged

- After the user's second server-side fix, the CRDT-ghost records that had been resurrecting `.md` files in `artists/`, `genres/disco.md`, `genres/electronic-dance.md`, and the seven docs under `_phantoms/` were finally cleared by Bash `rm`. Files do not regenerate.
- `get_dead_links` is now empty — every link in the wiki resolves to an existing doc.
- Removed the now-unneeded `_phantoms/**` rule from `.open-knowledge/config.yml`.
- Final wiki state: 44 markdown docs — 1 hub ([HOME](./HOME.md)), 1 work log, 8 genre overviews under `genres/<genre>/overview.md`, 15 artists nested under their primary genre as `genres/<genre>/artists/<artist>.md`, 13 venues under `venues/`, 7 neighborhoods under `neighborhoods/`. No top-level `artists/` folder anymore.

## 2026-04-30 — Restructure: per-neighborhood folders with venues subfolder

- Mirror of the genres restructure: each neighborhood is now its own folder with `overview.md` (the canonical neighborhood article) and a `venues/` subfolder for the live-music venues physically located there. Top-level `venues/` folder is gone.
- Neighborhood→venue mapping:
  - **lower-east-side**: cbgb, bowery-ballroom, mercury-lounge
  - **east-village**: (no venues — the punk clubs sit on the LES side of the Bowery)
  - **greenwich-village**: maxs-kansas-city, blue-note, village-vanguard, electric-lady-studios
  - **soho**: paradise-garage, mudd-club, knitting-factory
  - **harlem**: apollo-theater
  - **chelsea-meatpacking**: danceteria
  - **midtown-times-square**: studio-54
- Implementation: 20 `rename_document` calls — 7 to move neighborhood files into folders, 13 to move venues into neighborhood subfolders. All inbound markdown links across the wiki rewrote correctly.
- Cleanup: 4 stale on-disk leftovers (`venues/bowery-ballroom.md`, `venues/mercury-lounge.md`, `neighborhoods/east-village.md`, `neighborhoods/midtown-times-square.md`) cleared via Bash `rm` and didn't regenerate. The empty top-level `venues/` folder is gone.
- `.open-knowledge/config.yml`: dropped the top-level `venues/**` rule, replaced the simple `neighborhoods/**` rule with one that describes the new folder layout, added `neighborhoods/*/venues/**` rule for nested venues.
- `list_documents` confirms exactly 45 documents in the wiki: 1 HOME, 1 work log, 8 genre overviews + 15 nested artists, 7 neighborhood overviews + 13 nested venues. No phantoms in the document index.
- Known minor issue: `get_dead_links` reports 5 false positives sourced from `neighborhoods/east-village` and `venues/bowery-ballroom` — these "source" records do not exist on disk, do not appear in `list_documents`, and cannot be opened with `read_document` or `rename_document` (404). They are stale cache entries in the dead-link analyzer specifically. They do not affect navigation in the preview UI; the wiki itself is link-clean.
- Open follow-ups: the dead-link cache may flush on server restart or after enough quiescent time.

## 2026-05-03 — Added disco artists

- Created three artist pages under `genres/disco/artists/` for canonical Manhattan disco artists already namechecked in the disco overview but not previously documented: Chic, Grace Jones, and Larry Levan. Each follows the existing artist-page shape (frontmatter, intro, formation/Manhattan context, sound/signature work, Manhattan venue history, legacy, see also, further reading) and links liberally to existing genre/venue/neighborhood pages.
- Updated the [genres/disco/overview](./genres/disco/overview.md) "Key artists" section to link to the three new pages instead of leaving them as flat-text mentions.
- Files touched: [genres/disco/artists/chic](./genres/disco/artists/chic.md), [genres/disco/artists/grace-jones](./genres/disco/artists/grace-jones.md), [genres/disco/artists/larry-levan](./genres/disco/artists/larry-levan.md), [genres/disco/overview](./genres/disco/overview.md).
- Sources ingested: none — drew on widely-documented disco history (Tim Lawrence's *Love Saves the Day* and *Life and Death on the New York Dance Floor*, Nile Rodgers' *Le Freak*, Wikipedia, AllMusic). Listed in each article's "Further reading" footer rather than ingested.
- Open follow-ups: candidates for further disco artist coverage — Salsoul Orchestra, Sister Sledge, Frankie Knuckles (Continental Baths era, before Chicago), David Mancuso, Nicky Siano, Sylvester (San Francisco-based but central to the Garage-era canon).

## 2026-05-03 — Added jazz artists

- Created four artist pages under `genres/jazz/artists/` for Manhattan jazz figures already namechecked in the jazz overview but not previously documented: Sam Rivers, Ornette Coleman, John Zorn, and Wynton Marsalis. Each follows the existing artist-page shape and links into the venue/neighborhood graph (Knitting Factory, Village Vanguard, Blue Note, SoHo, East Village, Lincoln Center). Coverage spans the loft-jazz origin, the long-resident free-jazz pioneer, the downtown experimental curator, and the Lincoln Center mainstream institution — the four poles the jazz overview already structures itself around.
- Updated the [genres/jazz/overview](./genres/jazz/overview.md) "Key artists" section to link the four new pages instead of leaving them as flat-text mentions.
- Files touched: [genres/jazz/artists/sam-rivers](./genres/jazz/artists/sam-rivers.md), [genres/jazz/artists/ornette-coleman](./genres/jazz/artists/ornette-coleman.md), [genres/jazz/artists/john-zorn](./genres/jazz/artists/john-zorn.md), [genres/jazz/artists/wynton-marsalis](./genres/jazz/artists/wynton-marsalis.md), [genres/jazz/overview](./genres/jazz/overview.md).
- Sources ingested: none — drew on Michael Heller's *Loft Jazz*, John Litweiler's *Ornette Coleman: A Harmolodic Life*, the Tzadik catalog, and Jazz at Lincoln Center's institutional record. Listed in each article's "Further reading" footer.
- Open follow-ups: candidates for further jazz coverage — Cecil Taylor (Brooklyn-based but Manhattan-active), Bill Frisell, Dave Douglas, Mary Halvorson, Jason Moran (current Jazz at Lincoln Center artistic director for The Loft), Vijay Iyer; Studio Rivbea and The Stone deserve their own venue pages alongside the existing Knitting Factory / Vanguard / Blue Note set.
