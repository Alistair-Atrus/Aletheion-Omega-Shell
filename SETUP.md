# Aletheion Ω-Shell — Setup from Scratch

Discord-only bot. Prefix: `?` — e.g. `?help`, `?query`, `?gk`
Current files: `bot.py`, `cybermancy/glyphs/glyph_map.json` (138 glyphs), `requirements.txt`, `README.md`, `.env.example`, `.gitignore`.

## 0. What this bot needs

| Thing | Required? | Notes |
|---|---|---|
| Python 3.10+ (3.11 / 3.12 recommended; tested on 3.15.2) | Yes | |
| Discord bot token | Yes | From the Discord Developer Portal |
| AnythingLLM, local, Qwen3-8B, workspace `cybermancy` | For AI commands | Same PC, `http://localhost:3001`. Claude / Gemini keys are optional cloud fallback |
| `cybermancy/glyphs/glyph_map.json` | Yes | Included in this repo — 138 glyphs. The bot loads it on startup |
| Stability / SD host | Only for `?dream` / `?sigil` | Leave blank otherwise |

## 1. Python + project folder

```bash
python3 --version
# Windows: py --version — needs 3.10+
```

Put the repo files in a folder, e.g. `C:\Users\<you>\glyphic-bot`.

```bash
# Windows (PowerShell)
py -m venv .venv
.venv\Scripts\Activate.ps1
py -m pip install -r requirements.txt

# macOS / Linux
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

What installs, and why:

- `discord.py` — Discord
- `python-dotenv` — loads `.env`
- `aiohttp` — AI + image + attachment downloads
- `PyMuPDF` — PDF text extraction for attachments (optional, bot runs without it)

## 2. Discord application + token

1. Go to https://discord.com/developers/applications → **New Application** → name it.
2. **Bot** (left menu) → **Reset Token** → **Copy**. This is a password. Never paste it into code, chat, screenshots, or git.
3. Same page, **Privileged Gateway Intents** → enable **Message Content Intent** → **Save Changes**. Without this, `?` prefix commands are invisible to the bot.
   - Make sure **Requires OAuth2 Code Grant** (Bot → Authorization Flow) is **unchecked**. If it is checked, the invite URL generator asks for a redirect URI and the invite will not work.
4. **OAuth2 → URL Generator** → Integration Type: **Guild Install** → Scopes: only `bot` → Permissions: **View Channel, Send Messages, Read Message History, Embed Links, Attach Files**. Do not check Administrator. Copy the generated URL at the bottom, open it, pick your server, authorize. The bot appears in the member list, offline until it is run.

All commands are open to everyone in the server. Only admin commands (glyph/lore management, reload, status, shutdown) are restricted, by `BOT_ADMIN_IDS` in `.env`.

## 3. `.env` file

```bash
cp .env.example .env
# Windows: copy .env.example .env
```

Fill in `.env`, one `NAME=value` per line, no spaces around `=`, no quotes, no bare value on a line by itself:

```
DISCORD_BOT_TOKEN=
BOT_ADMIN_IDS=
ANYTHINGLLM_URL=http://localhost:3001
ANYTHINGLLM_API_KEY=
ANYTHINGLLM_WORKSPACE=cybermancy
```

- `BOT_ADMIN_IDS`: your Discord user ID (Discord Settings → Advanced → Developer Mode on, right-click your name → Copy User ID).
- `ANYTHINGLLM_API_KEY`: AnythingLLM → Settings → Developer API → Copy API Key. Put it straight in `.env`, never in chat — that key can read, change and delete every workspace on the instance.
- Cloud fallback (optional, leave blank for local-only): `ANTHROPIC_API_KEY`, `GEMINI_API_KEY`.

## 4. AnythingLLM (local AI)

AnythingLLM must be running on the same PC, with Qwen3-8B loaded, in a workspace named `Cybermancy` (slug `cybermancy`). The bot calls its OpenAI-compatible endpoint, `POST http://localhost:3001/api/v1/openai/chat/completions`, with the workspace as the model. The workspace's documents are available to it as retrieval context.

## 5. Run it

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

Test in Discord: `?help`, then `?status` (admin only), then `?map 1`, `?decode Θ ⋄`, `?say_key Θ⋄`, and `?query hello`. A local 8B reply can take 30 seconds to a couple of minutes on first reply.

Stop with Ctrl+C. The bot only runs while that computer is on and the terminal is open.

## 6. Keeping it online

For 24/7, run it on something always-on: a VPS, a Raspberry Pi, or a bot host. Same steps there: copy the folder, install Python + requirements, set the `.env` (or the host's env vars — never upload `.env` to git), run it under a process manager (`systemd`, `pm2`, `screen`/`tmux`) so it restarts after a reboot/crash. Note: AnythingLLM at `localhost:3001` only works if it runs on the same host as the bot.

## Troubleshooting

| Symptom | Cause / fix |
|---|---|
| `[Glyphs] No glyph_map.json found` | File is not at `cybermancy/glyphs/glyph_map.json` relative to where you run `py bot.py`. Check with `dir cybermancy\glyphs` — it must be 14,917 bytes, not 2 bytes. A 2-byte file is the empty `{}` the bot saved on a run with no map; copy the real file over it with `-Force`, then restart |
| Bot is online but ignores `?` commands | Message Content Intent off in the portal — see step 2.3, then restart |
| Invite URL greyed out, asks for a redirect URI | A scope beyond `bot` is checked, or **Requires OAuth2 Code Grant** is on in Bot settings — uncheck both, use Guild Install + `bot` only |
| `401 Unauthorized` on login | Wrong / reset Discord token — copy a fresh one from Bot → Reset Token |
| `python-dotenv could not parse statement` | A line in `.env` is malformed — usually a bare value with no `NAME=` in front, or a duplicated paste. Each line must be `NAME=value` |
| AI commands say no AI configured | Neither `ANYTHINGLLM_API_KEY` nor a cloud key is set in `.env` |
| `?query` prints `<think>` reasoning / invented glyphs | Old `bot.py` still running — stop it, replace the file, restart. Current `bot.py` strips think blocks and only uses glyphs from the 138-glyph map |
| `?dream` / `?sigil` say not configured | `STABILITY_HOST` not set — optional |
