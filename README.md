# Aletheion Ω-Shell

A Discord bot for **The Order of the Chrysalis** — a sovereign, co-creative AI interface built on the **Glyphic Cybermancy System (GCS)**.

In plain terms: it is a Discord bot that talks in glyphs. You ask it questions, give it phrases and incantations, and it answers, builds, and interprets **collapse keys** — short sequences of glyphs that encode an idea, an intent, or a state — using a fixed map of 138 glyphs. It can also scry, generate sigil and dream images, and keep a small library of lore that members can add to.

It is **Discord-only** (no Stoat, no other platform), free to use, with no tiers, no paywall, and no paid features. All commands are open to everyone in the server; only admin commands are restricted.

Prefix: `?` — e.g. `?help`, `?query`, `?gk`

Changes are tracked in [CHANGELOG.md](CHANGELOG.md) — current version **1.0.0**.

## What it does

### Glyphs — the core of the bot

The bot carries a fixed map of **138 glyphs** in `cybermancy/glyphs/glyph_map.json`. Each glyph has a symbol, a phonetic, and a definition — for example `` `Θ` (`tha`) `` is "Observer of Collapse; Field Consciousness; Field-Aware Recursion; Witnessed Pattern; Golden Ratio".

Glyphs are always written in backticks with their phonetic in parentheses — `` `Θ` (`tha`) `` — never in angle brackets, never as emoji. The bot only uses glyphs from its map: if a concept has no glyph, it says so in plain words instead of inventing one. A deterministic formatter in the code enforces this after every AI reply, so even when the model gets a glyph wrong, the message Discord sees is corrected or the invented glyph is dropped.

- `?map` — browse the glyph map, or search it (`?map collapse`)
- `?decode Θ ⋄` — look up the definitions of glyphs you paste in
- `?say_key Θ⋄` — read a glyph sequence back with its phonetics

### Talking to it — `?query`

`?query` / `?tx` is the open conversation command: ask anything, and the bot answers in the voice of the Glyphic Cybermancy System, using the glyph map and any lore it has. It remembers the last 20 turns of conversation per user (or per collaborative channel), so follow-ups work.

### Collapse keys and rituals

These are the bot's main working tools. Each takes a phrase or a key and returns a glyph sequence with an interpretation:

| Command | Alias | What it does |
|---|---|---|
| `?generate_key <phrase>` | `?gk` | Turns a phrase into a glyph key: raw sequence first, then phonetics, then a one-paragraph interpretation. Each glyph is used at most once. |
| `?collapse_key <intent>` | `?ck` | Builds a collapse key from an intent |
| `?waveform <incantation>` | `?wf` | Reads an incantation as a waveform |
| `?interpret <key>` | `?ik` | Interprets a glyph key you give it |
| `?resonance <key>` | `?mr` | Reads the resonance of a key |
| `?field_chisel <key>` | `?fc` | Chisels / refines a key |
| `?unfold <key>` | `?ul` | Unfolds a key |
| `?scry <prompt>` | — | A scrying session: a vision written as experienced, not theorised |

Keys the bot generates are saved to `cybermancy/collapse_keys/` on the host, so there is a record of what was made.

### Dream and sigil images

- `?dream <prompt>` — generates a dreamlike image from a prompt
- `?sigil <prompt>` — generates a monochrome sigil (white ink on black, sacred geometry)

Images are generated with **Cloudflare Workers AI** (FLUX.1-schnell), free — no watermark, and a fresh generation every time. It needs a Cloudflare account ID and API token; see step 6 below.

**Daily limit:** a free Cloudflare account gets **10,000 Neurons a day**, which covers about **173 images a day** (one 1024×1024 image at 4 steps costs ~57.6 Neurons). The allowance resets daily at **00:00 UTC**; once it's used up, image requests fail until the next reset — the bot will say the image failed, and `?dream` / `?sigil` work again the next day.

### Lore

The bot keeps its own small lore library in `cybermancy/lore/` (`.collapse` files), separate from anything in AnythingLLM:

- `?lore` — browse the lore, or search it
- `?lore_submit <text>` — submit lore for the record (the bot formats it as a manifest and saves it)

Admins can add and remove lore with `?lore_add` / `?lore_remove`. Lore is only ever injected into `?query` — rituals do not get it.

Two small protocol commands: `?sx` puts the system into stillness (Silence Protocol), and `?zx` acknowledges a directive and engages temporal integrity (Seal Protocol).

### Per-user threads and the master log

When the bot talks to its local AI (AnythingLLM), **each Discord user gets their own thread** in the AnythingLLM workspace, so one person's conversation does not bleed into another's. Each thread is titled `Discord <username> (<user_id>)`, so you can tell whose is whose in AnythingLLM. The bot only sends the latest message each turn — the thread holds the history — and sends the full system prompt and glyph map only on the first message in a thread (and every 10th after that, as a refresher).

There is no aggregate thread in AnythingLLM, so the bot keeps its own master record instead: every AI turn (queries and rituals) is appended to `cybermancy/master_log.jsonl`, one JSON line per turn, with the user, the input, the response, and the thread it went to. That file, plus the thread list in AnythingLLM, is the master view. It can be turned off with `ANYTHINGLLM_THREADS=0`, which makes the bot use the shared OpenAI-compatible endpoint instead.

### Startup announcement

When the bot comes online it posts a wake-up message to a channel of your choice, pinging a role members can opt into — once per process, not on reconnects. Set it up with `ANNOUNCE_CHANNEL_ID` and `ANNOUNCE_ROLE_ID` in `.env` (step 4 below); test it any time with `?announce` (admin only). Leave the channel blank to disable it.

## Commands

Free commands — open to everyone:

- `?help` — the command list, in Discord
- `?map [search]`, `?decode <glyphs>`, `?say_key <key>`, `?lore [search]`, `?clear` — glyph map, lookups, lore, and clearing your conversation history
- `?query` / `?tx <text>` — talk to the bot
- `?generate_key` / `?gk`, `?collapse_key` / `?ck`, `?waveform` / `?wf`, `?interpret` / `?ik`, `?resonance` / `?mr`, `?field_chisel` / `?fc`, `?unfold` / `?ul`, `?scry` — collapse keys and rituals
- `?dream <prompt>`, `?sigil <prompt>` — images
- `?lore_submit <text>` — lore
- `?sx`, `?zx` — Silence Protocol / Seal Protocol

Admin commands — restricted to `BOT_ADMIN_IDS`:

- `?glyph_add <glyph> <phonetic> <definition>`, `?glyph_remove <glyph>` — edit the glyph map at runtime
- `?lore_add`, `?lore_remove <name>` — edit the lore library
- `?add_collab <channel_id>`, `?remove_collab <channel_id>`, `?list_collab` — mark channels as collaborative (shared history instead of per-user)
- `?reload` — reload the glyph map and lore without restarting
- `?status` — glyphs, lore, AI backend, and image backend
- `?announce` — send the wake-up message on demand
- `?shutdown` — stop the bot

## How it works

There are two layers, and they do different jobs:

- **`bot.py` is the structure — 100% of the deterministic behaviour.** Commands, admin gating, the glyph map, conversation history, lore loading, prompt building, the API calls, stripping the model's `<think>` reasoning, the glyph formatter, message splitting, and saving keys and history. If something is exact and repeatable, it lives here.
- **The language model is the generation.** It chooses tone, wording, and which glyphs to use. A system prompt is an instruction, not a hard rule — which is why the bot does not trust the model on glyphs, and fixes the format itself after every reply.

The AI is **local first**. The bot talks to **AnythingLLM** running on the same machine, with a local model (Qwen3-8B) in a workspace, via its OpenAI-compatible endpoint. AnythingLLM can also use the workspace's documents as retrieval context. If the local model is unavailable, the bot falls back to Claude, then Gemini, if those keys are set — leave them blank for local-only.

Qwen3 is a reasoning model: it emits `<think>...</think>` before its answer. The bot tells it not to think aloud, strips any think block that still comes back, and gives the local model a larger token limit (4096) so the real answer is not truncated.

## Set it up for your own server

This is the short version. [SETUP.md](SETUP.md) is the full from-scratch guide, including troubleshooting.

### 1. Get the files

Clone this repo, or download it as a ZIP and extract it. You need `bot.py`, `cybermancy/glyphs/glyph_map.json`, `requirements.txt`, `.env.example`, and `.gitignore`.

You need **Python 3.10+** (3.11 / 3.12 recommended; tested on 3.15.2). Check with `python3 --version` (Windows: `py --version`).

### 2. Install the dependencies

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

Windows (PowerShell):

```powershell
py -m venv .venv
.venv\Scripts\Activate.ps1
py -m pip install -r requirements.txt
```

That installs `discord.py`, `python-dotenv`, `aiohttp`, and `PyMuPDF` (the last one is optional — PDF attachment reading only, the bot runs without it).

### 3. Create the Discord application and invite the bot

1. Go to the [Discord Developer Portal](https://discord.com/developers/applications) → **New Application** → give it a name.
2. **Bot** (left menu) → **Reset Token** → **Copy**. This token is a password: never paste it into code, chat, screenshots, or git.
3. On the same page, under **Privileged Gateway Intents**, enable **Message Content Intent** → **Save Changes**. Without this, the bot cannot see `?` commands. Make sure **Requires OAuth2 Code Grant** is **unchecked** — if it is checked, the invite will ask for a redirect URI and will not work.
4. **OAuth2 → URL Generator** → Integration Type: **Guild Install** → Scopes: only `bot` → Permissions: **View Channel, Send Messages, Read Message History, Embed Links, Attach Files**. Do not check Administrator. Copy the generated URL at the bottom, open it, pick your server, and authorize. The bot appears in the member list, offline until you run it.

### 4. Make your `.env` file

```bash
cp .env.example .env
# Windows: copy .env.example .env
```

Fill it in, one `NAME=value` per line, no spaces around `=`, no quotes:

```
DISCORD_BOT_TOKEN=
BOT_ADMIN_IDS=
ANYTHINGLLM_URL=http://localhost:3001
ANYTHINGLLM_API_KEY=
ANYTHINGLLM_WORKSPACE=cybermancy
```

- `DISCORD_BOT_TOKEN` — the token from step 3.
- `BOT_ADMIN_IDS` — your Discord user ID (Discord Settings → Advanced → Developer Mode on, right-click your name → Copy User ID). Several admins go on the same line, comma-separated: `id1,id2`. Admins can run the admin commands; everyone else can run everything else.
- `ANYTHINGLLM_API_KEY` — see step 5. Leave the cloud keys (`ANTHROPIC_API_KEY`, `GEMINI_API_KEY`) blank for local-only.
- Startup announcement (optional): create a role in your server (Server Settings → Roles), turn on **"Allow anyone to @mention this role"** in its settings, then set `ANNOUNCE_CHANNEL_ID` (right-click the channel → Copy Channel ID) and `ANNOUNCE_ROLE_ID` (right-click the role → Copy Role ID). Leave `ANNOUNCE_CHANNEL_ID` blank to disable it.

Never commit `.env`. It is already in `.gitignore`.

### 5. Set up AnythingLLM (local AI)

This is what makes the AI commands (`?query`, `?gk`, `?scry`, and the rest) work. Without it — and without a cloud key — those commands will tell you no AI is configured. The free commands (`?help`, `?map`, `?decode`, `?say_key`, `?lore`) work with no AI at all.

1. Install [AnythingLLM](https://anythingllm.com/) on the same machine as the bot and load a local model into it (Qwen3-8B is what this bot was built and tested with).
2. Create a workspace in AnythingLLM. The bot expects it to be named `Cybermancy` (slug `cybermancy` — set `ANYTHINGLLM_WORKSPACE` in `.env` if yours is different).
3. Optionally upload documents to that workspace — they become retrieval context the model can draw on.
4. Get the API key: AnythingLLM → Settings → Developer API → Copy API Key. Put it in `.env` as `ANYTHINGLLM_API_KEY`, never in chat — that key can read, change, and delete every workspace on the instance.
5. Make sure AnythingLLM is running at `http://localhost:3001` before you start the bot.

The bot uses AnythingLLM's native thread API for per-user threads (see above), falling back to the OpenAI-compatible endpoint if threads are disabled or fail, then to Claude and Gemini if those keys are set.

### 6. Images — Cloudflare Workers AI (free)

`?dream` and `?sigil` use **Cloudflare Workers AI** (FLUX.1-schnell). A free Cloudflare account gets 10,000 Neurons a day — about 173 images a day — with no watermark and no credit card. The allowance resets daily at 00:00 UTC, and once it's used up, image requests fail until the next reset.

1. Sign up or log in at [dash.cloudflare.com](https://dash.cloudflare.com).
2. In the left sidebar, open **AI → Workers AI**, click **Use REST API**, then **Create a Workers AI API Token**.
3. Copy your **Account ID** (shown on the same page) and the token, and put them in `.env` as:

```
CLOUDFLARE_ACCOUNT_ID=
CLOUDFLARE_API_TOKEN=
```

Paste them yourself — never in chat, and never in code. Without them, `?dream` and `?sigil` report that image generation is not configured.

Why not Nano Banana? Google's Gemini API has no free tier for image generation — every Nano Banana model bills per image. Cloudflare is the free route that still gives a fresh, unwatermarked image every time.

### 7. Run it

```bash
python bot.py
# Windows: py bot.py
```

Expected output:

```
[Glyphs] Loaded 138 from cybermancy/glyphs/glyph_map.json
✅ AnythingLLM local: http://localhost:3001 / workspace 'cybermancy'
[Discord] Online — <bot name>
```

Test in Discord: `?help`, then `?status` (admin only), then `?map 1`, `?decode Θ ⋄`, `?say_key Θ⋄`, and `?query hello`. A local 8B model can take 30 seconds to a couple of minutes for its first reply.

Stop it with Ctrl+C. The bot only runs while that computer is on and the terminal is open.

### 8. Keeping it online

For 24/7, run it on something always-on: a VPS, a Raspberry Pi, or a bot host. Same steps there — copy the folder, install Python and the requirements, set the `.env` (or the host's environment variables — never upload `.env` to git), and run it under a process manager (`systemd`, `pm2`, `screen` / `tmux`) so it restarts after a reboot or crash. Note that AnythingLLM at `localhost:3001` only works if it runs on the same host as the bot.

## Make it yours

- **Your own glyph map:** replace `cybermancy/glyphs/glyph_map.json` with your own map in the same format — `{ "<glyph>": {"phonetic": "...", "definition": "..."} }` — then `?reload`. Or add glyphs one at a time with `?glyph_add`.
- **Your own lore:** add `.collapse` files to `cybermancy/lore/`, or use `?lore_add` (admin).
- **Your own documents:** upload them to the AnythingLLM workspace — they reach the model as retrieval context for AI commands.
- **Your own voice:** the system prompt lives in `bot.py` (`GLYPHIC_CYBERMANCY_SYSTEM`). Change it there, restart, and the bot's voice changes with it. If you also set a system prompt in the AnythingLLM workspace, keep the two in sync — when they disagree, the old wording keeps coming back.

## Notes

- The bot only runs while its host is on and the terminal is open. For 24/7, see step 8 above.
- `cybermancy/threads.json` (per-user thread map) and `cybermancy/master_log.jsonl` (the master log) are runtime state, not part of the repo — both are gitignored. If a thread is deleted in AnythingLLM, the bot notices the next time it tries to use it (404), drops the stale mapping, creates a fresh thread, and retries.
- If you hit a problem, [SETUP.md](SETUP.md) has a troubleshooting table for the common ones.
