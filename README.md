# Aletheion Ω-Shell

Discord bot for The Order of the Chrysalis — the Glyphic Cybermancy System (GCS), a sovereign co-creative AI interface. Prefix: `?`

Discord-only. Python, `discord.py`.

## What it does

All commands are open to everyone in the server. Admin commands are restricted to bot admins (`BOT_ADMIN_IDS`).

- `?help`, `?map`, `?decode`, `?say_key`, `?lore` — no AI key needed
- `?query` / `?tx`, `?generate_key` / `?gk`, `?waveform` / `?wf`
- `?collapse_key` / `?ck`, `?interpret` / `?ik`, `?resonance` / `?mr`, `?field_chisel` / `?fc`, `?unfold` / `?ul`, `?scry`, `?sigil`, `?dream`
- `?lore_submit`, `?sx`, `?zx`
- Admin: `glyph_add` / `glyph_remove`, `lore_add` / `lore_remove`, `add_collab` / `remove_collab` / `list_collab`, `reload`, `status`, `shutdown`

Glyphs are introduced as `` `Θ` (`tha`) `` — backticks, never angle brackets, never emoji as glyphs. Only glyphs in the map are used; a concept with no glyph is stated in plain words, not invented.

## AI — local first

AnythingLLM on the same PC, Qwen3-8B in the `cybermancy` workspace, via its OpenAI-compatible endpoint (`POST /api/v1/openai/chat/completions`, workspace as model). Tried first. Claude second, Gemini third, if keys are set. Leave the cloud keys blank for local-only.

Qwen3 is a reasoning model: the bot tells it not to output `<think>` reasoning, strips any think block that still comes back, and raises the local token limit to 4096 so the final answer is not truncated.

## Glyph map

`cybermancy/glyphs/glyph_map.json` — 138 glyphs, from the authoritative `Glyph_map.txt`. Format: `{ "<glyph>": {"phonetic": "...", "definition": "..."} }`. The bot loads it on startup; use `?reload` after replacing it. Add glyphs at runtime with `?glyph_add`.

## Setup

See [SETUP.md](SETUP.md) for the full from-scratch guide: Python 3.10+, venv, Discord Developer Portal application/token, Message Content Intent, OAuth2 invite, `.env` setup, and running the bot.

Quick start:

```bash
python -m venv .venv
# Windows: .venv\Scripts\Activate.ps1
pip install -r requirements.txt
cp .env.example .env
# fill in DISCORD_BOT_TOKEN, BOT_ADMIN_IDS, ANYTHINGLLM_API_KEY, ANYTHINGLLM_WORKSPACE
python bot.py
```

Never commit `.env`. Never paste tokens in chat or code.

## Status / notes

- The bot only runs while its host PC is on and the terminal is open. For 24/7, run it on an always-on host under a process manager.
