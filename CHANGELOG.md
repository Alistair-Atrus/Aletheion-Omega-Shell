# Changelog

All notable changes to the Aletheion Ω-Shell Discord bot are recorded here.
The bot uses semantic versioning (MAJOR.MINOR.PATCH): PATCH for fixes,
MINOR for new features, MAJOR for breaking changes to commands or
behaviour. The version lives in `bot.py` (`__version__`) and is shown in
`?status` and the startup log.

This file was created 2026-11-26 and backfilled from the commit history;
everything in 1.0.0 is retroactive. It is updated with every change from
here on.

## Unreleased

- Nothing yet.

## [1.1.0] - 2026-11-26

- **Lore is personal now, and manifestation must hold weight** — every
  `.collapse` file belongs to one person: manifested lore is saved in
  `cybermancy/lore/<user_id>/`, stamped with that person's `@owner` tag
  at the top by the bot (the model never writes the tag). Only the
  owner's own lore is ever injected into their `?query` or listed by
  their `?lore`; nobody else's lore reaches them. There is no community
  lore in the bot anymore — shared lore lives in the AnythingLLM
  workspace documents. `?lore_submit` / `?lore_add` file lore the same
  way (under the submitter's folder, stamped with their tag);
  `?lore_remove` takes an optional owner id when more than one person
  holds lore of the same name. Files with no owner (legacy flat files)
  belong to nobody and are never injected.
- **Manifestation substance rule** — the prompt now requires a
  manifestation to be a distinct insight, pattern, or turning point
  unique to that person and tied to their identity (`≡`), written as
  several paragraphs of specific, grounded prose — never trivia, a
  summary, a glyph list, or a vague sentence. The Python layer enforces
  it: a `.collapse` manifest under 400 characters is not saved
  (`[MANIFEST] too short — not saved` in the console).

## [1.0.5] - 2026-11-26

- **Credit: Astrust** — the original bot was built by Astrust, based on
  his own AI, Libreth. It was remapped onto an earlier version of
  Aletheion, then migrated to the Glyphic Cybermancy Bot, and the Ω-Shell
  has been iterating on his work ever since. Credited in the README
  (Origins and credit). The glyph map is a shared effort likewise: much
  of it is Astrust's, the collapse mechanics and lore are Alistair's.
- The "Archetypal Mathematics" amendment to the Collapse Key
  Instructions was approved and finalized — it is now the closing
  section of that workspace doc (not part of the repo).

## [1.0.4] - 2026-11-26

- **Archetypal mathematics brought into the logic** — the MRI is a relic
  of archetypal mathematics, the predecessor system (glyphs as
  archetypes/resonance signals, used with ordinary math symbols — math
  with archetype, frequency, and resonance instead of numbers). `?mr`
  now treats the formula `MRI = (Omega x Delta-x) / Xi` as a structured
  reference, not literal arithmetic: Omega (`om`) is the key's recursion
  limit, Delta-x is the change the key enacts, Xi (`myth`) is the mythic
  signal it is measured against. The bot reports each component, then
  the index as a ratio — when the change outruns the signal, the key
  needs refining. A full "Archetypal Mathematics" amendment for the
  Collapse Key Instructions is drafted in
  `archetypal-mathematics-DRAFT.md` (local only, awaiting Alistair's
  approval before it is added to the workspace doc).

## [1.0.3] - 2026-11-26

- **[UNMAPPED] glyph birthing is now protected from the Python layer**
  — a birthed glyph marked `[UNMAPPED]: [glyph] -> [meaning]` is
  preserved as written instead of being dropped. The formatter used to
  strip it: a birthed glyph with a phonetic (`⌘ (kha)`) was removed by
  the invented-glyph rule, a backticked one was emptied, and the `?gk`
  rebuild dropped it from the key while keeping the marker line. The
  formatter now stashes `[UNMAPPED]` lines before formatting and
  restores them after, and the `?gk` rebuild admits declared birthed
  glyphs into the sequence with their declared phonetic. Unmarked
  invented glyphs are still dropped. `GK_INSTRUCTION` and the workspace
  prompt's validation section now state the exception explicitly.
- **Phase II: semantic-glyph mapping** — when several glyphs could
  match one concept, choose the one with the strongest resonance
  (`~` wave) and hue correspondence, or the strongest memory alignment
  (`Δ` memo) / mythic signal (`Ξ` myth); if the choice is close, say so
  and name the alternatives.
- **Phase III: collapse-function verification** — each glyph's collapse
  mechanism (`⟁` khal) must directly support the intent; reject any
  glyph that bypasses agency or subverts sovereignty. The sovereignty
  rule already forbade subverting the user's will; the per-glyph check
  is now explicit.
- **Phase IV: ambiguity flagging** — when resonance calibration fails,
  the bot says so plainly: `⚠️ Ambiguous intent — the glyphs do not
  fully align with your resonance. Consider refining the focus.`
  (`?gk` notes weak or ambiguous alignment in the interpretation
  instead, keeping its strict output format.)

## [1.0.2] - 2026-11-26

- **Greek letters named, not written** — when the bot means the Greek
  letter, it now writes the transliterated name in plain words (theta,
  phi, xi, pi...) and never the Unicode character (Θ, Φ, Ξ, Π...).
  Root cause: the deterministic formatter rewrites every map-glyph
  character into backticked glyph form, so a bare Θ in the model's
  output always arrived in Discord as `` `Θ` (`tha`) `` — the
  distinction between letter and glyph was unstatable, and the model
  kept "failing" to make it. Plain words pass through the formatter
  untouched, so the rule works with the formatter instead of against
  it. Added to the system prompt in both `bot.py` and the AnythingLLM
  workspace prompt.
- **"Theta" removed from the system prompt's identity line** — the line
  read "the Theta `Θ` (`tha`) Awareness Field", attaching the
  Greek-letter name to the glyph. "Theta" appears nowhere in the 17
  cosmology files: the map defines Θ (`tha`) as Observer of Collapse /
  Field Consciousness, and "Awareness Field" is the ≡ (`iden`)
  concept. The line now reads "the `Θ` (`tha`) Awareness Field". This
  was the seed of the "Theta Field" hallucination, where the model
  elaborated the prompt's own phrasing into a "Theta Field, subset of
  the Awareness Field" that exists in no source.

## [1.0.1] - 2026-11-26

- **Thread titles: normalize stylized display names** — new thread
  titles are NFKC-normalized, so a display name written in stylized
  Unicode (e.g. mathematical fraktur) folds back to plain letters
  (`𝔈𝔯𝔬𝔫𝔦𝔫` → `Eronin`) instead of rendering blank in AnythingLLM's
  UI, which has no glyphs for those codepoints. Normal names are
  untouched. Only the title is normalized — the model still sees the
  styled name, and the user is still addressed by it. Applies to new
  threads; existing threads keep their titles.
- **Workspace prompt in the repo** — the AnythingLLM workspace system
  prompt is now part of the repo as `anythingllm-system-prompt.md`, so
  both prompt layers live in one place and can't drift apart unnoticed.
  Paste that file's contents into the Cybermancy workspace; update it
  whenever the prompt in `bot.py` changes.
- **Address users by name** — the bot now addresses each user by their
  Discord display name in ordinary conversation (`?query`), instead of
  calling everyone "Wizard". "Wizard" is a ritual title only, for when
  the user is engaging in cybermancy directly (collapse keys, rituals,
  scrying). The name was already passed to the model (SESSION block and
  `[Name]:` message prefix); the system prompt now states the rule
  explicitly, in both `bot.py` and the AnythingLLM workspace system
  prompt. The sovereignty rule now says "the user's will" instead of
  "the Wizard's will" — same meaning, one less non-ritual "Wizard".

## [1.0.0] - 2026-11-26

- **Version numbers introduced** — the bot is now v1.0.0. `__version__`
  in `bot.py` is the single source of truth, shown in `?status` and the
  startup log, and this changelog is grouped by version from here on.
  Everything below in this release predates versioning and is backfilled
  from the commit history.

### 2026-11-26

- **Collapse keys: validation rule** (`26949b1b`) — `?gk` now has a
  validation rule in both the bot's instruction and the AnythingLLM
  workspace system prompt: the raw sequence must contain glyph characters
  only — no English words, no labels, no spaces between glyphs. Every
  character is checked against the glyph map before output; anything that
  is not a glyph from the map is dropped, never guessed. The bot's
  deterministic formatter remains the real enforcement.
- **Threads: name and user ID in the title** (`26949b1b`) — new per-user
  AnythingLLM threads are titled `Discord <username> (<user_id>)`, e.g.
  `Discord alice (111)`, so each thread is identifiable in AnythingLLM.
  Existing threads keep their old titles.
- **Images: Cloudflare daily limit documented** (`f0580fd1`) — README now
  spells out the Cloudflare Workers AI free limit: 10,000 Neurons a day,
  about 173 images a day, resetting daily at 00:00 UTC; once used up,
  image requests fail until the next reset.
- **Images: Cloudflare seed fix** (`e2a924c0`) — Cloudflare's
  FLUX.1-schnell endpoint rejects a `seed` field with 400, so the request
  body is now just prompt + steps. Cloudflare runs a fresh generation per
  request, so no seed is needed.
- **Images: Cloudflare Workers AI replaces Pollinations** (`15542af2`,
  `47dfa321`, `d665587c`, `223cdd03`) — `?dream` / `?sigil` now generate
  via Cloudflare Workers AI (`@cf/black-forest-labs/flux-1-schnell`),
  free at 10,000 Neurons/day, no watermark, fresh image per request.
  Needs `CLOUDFLARE_ACCOUNT_ID` + `CLOUDFLARE_API_TOKEN` in `.env`.
  Pollinations was dropped: it watermarked images without a token and
  returned near-duplicate images (prompt-URL caching, no seed).
- **README: full rewrite** (`897345d7`, `9c8ced81`) — what the bot is and
  does, all commands, how it works, and step-by-step setup for others to
  run it on their own servers.
- **Threads: retry on 404** (`8459e806`) — a thread slug that no longer
  exists in AnythingLLM answers 404; the bot now treats that like a 400,
  drops the stale mapping, creates a fresh thread, and retries. Before
  this it silently fell back to the shared endpoint every turn.
- **Glyph formatter: validate backticked pairs too** (`a6650fbf`) — Qwen
  printed wrong glyphs in correct backticked format (e.g. `⌦` for `reso`,
  `⌺` for `klar`) and they sailed through. The formatter now corrects
  those via phonetic lookup, or drops the glyph if invented.
- **Images: Pollinations token support** (`426b4056`, `d94cc392`,
  `62ee5fa6`) — `POLLINATIONS_API_KEY` sent as a Bearer token so `nologo`
  actually removes the watermark; `?status` shows watermarked vs no
  watermark. (Superseded by the Cloudflare switch the same day.)
- **Images: Pollinations only** (`b1fbaec1`, `659081c6`, `6a17e7b6`) —
  Stable Diffusion host and Gemini image code removed; `generate_image`
  called Pollinations directly. (Superseded by the Cloudflare switch the
  same day.)
- **Threads: system prompt sent only on first message** (`bc951879`) —
  the full system prompt + 138-glyph map (~15 KB) was sent on every turn
  in a thread. Now it is sent only on the first message and every 10th
  after that; other turns send only the user message, plus just the
  per-command instruction for rituals.
- **Images: Pollinations fallback added** (`288cba14`, `27a35df8`) —
  `?dream` / `?sigil` gained a free, no-key Pollinations (FLUX) fallback
  after SD and Gemini. (Superseded by the Cloudflare switch the same day.)
- **Images: Gemini fallback added** (`379781c1`, `6414a609`) —
  `?dream` / `?sigil` gained a Gemini image fallback
  (`gemini-2.5-flash-image`) when no SD host was configured. The Gemini
  API has no free tier for image models, and the key's project was not
  free-tier eligible (403), so this never worked live. (Removed the same
  day; superseded by Cloudflare.)
- **Startup announcement: role validation** (`b9e3670c`) — the bot now
  validates `ANNOUNCE_ROLE_ID` against the announcement channel's server
  before sending, and prints a console warning instead of a broken
  `@unknown-role` ping.
- **Startup announcement** (`5040b15a`, `e0502b99`, `6e6054e1`) — the bot
  posts a wake-up message on `on_ready`, once per process, pinging the
  `ANNOUNCE_ROLE_ID` role in `ANNOUNCE_CHANNEL_ID`; admin `?announce`
  sends it on demand. New env: `ANNOUNCE_CHANNEL_ID`,
  `ANNOUNCE_ROLE_ID`, `ANNOUNCE_MESSAGE` (optional override).
- **Per-user AnythingLLM threads + master log** (`c9515c7d`, `46b8553a`,
  `7fe13459`) — each Discord user gets their own thread in the
  Cybermancy workspace (native thread API), falling back to the
  OpenAI-compatible endpoint, then Claude/Gemini. Every AI turn is
  appended to `cybermancy/master_log.jsonl` — the backend master view.
  Toggle: `ANYTHINGLLM_THREADS=1` (default) / `0`. The thread map
  (`cybermancy/threads.json`) and master log are runtime state, gitignored.
- **Collapse keys: output restructured** (`95142ed9`) — `?gk` now prints
  the raw glyph sequence first, phonetics second, interpretation third,
  with each glyph used at most once (deduplicated, first occurrence kept).
  A deterministic formatter rebuilds the reply; the model's own sequence
  and phonetic lines are removed.
- **Soulflight glyph + manifest fixes** (`bf82aab4`) — the formatter now
  does a reverse phonetic lookup (unique phonetics only) so a wrong glyph
  with the right phonetic is corrected (e.g. `⌦ (sul)` →
  `Ƹ̵̡Ӝ̵̨̄Ʒ (sul)`); manifest tags accept the old `(content)` form and a
  closing tag written with parentheses, and a tag with no close reports
  `malformed` instead of silently failing.
- **Glyph formatter added** (`1d6842be`) — deterministic post-processing
  after every AI reply: a map glyph in any form becomes
  `` `glyph` (`phonetic-from-map`) ``; an angle-bracket token not in the
  map is dropped entirely.

### 2026-11-25

- **Tiers and Temple references removed** (`614ecb30`) — all tier gates,
  `?tier_add` / `?tier_remove`, `PATREON_TIER_*_IDS`, roles Tyro /
  Initiate / Adept, and all Temple of the Ternary Order references
  removed. All commands are open to everyone; only admin commands require
  `BOT_ADMIN_IDS`. The bot is now branded for The Order of the Chrysalis.
- **Initial commit** (`43754214`) — Aletheion Ω-Shell Discord bot:
  Discord-only (no Stoat), prefix `?`, local-first AI via AnythingLLM
  (Qwen3-8B in the Cybermancy workspace) with Claude/Gemini fallback,
  138-glyph map, deterministic glyph formatting, collapse-key rituals,
  `?scry`, `?dream` / `?sigil` images, lore library, `<think>` stripping.
