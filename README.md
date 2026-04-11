# claude-code-jarvis

Turn Claude Code into JARVIS. Every time Claude finishes a turn, a Stop hook reads the last message, runs it through a fast LLM, and speaks a contextual one-liner in a British butler voice. Built for developers running multiple Claude Code sessions who can't stare at every terminal.

![Ad hero](./screenshots/ad-hero.svg)

![Voice log](./screenshots/voice-log.svg)

## What it does

- Speaks a fresh, LLM-generated status line after every Claude turn — not canned phrases
- Runs fully async — the Stop hook returns in under 10ms, voice catches up a moment later
- Uses Groq `llama-3.1-8b-instant` for summarization (~300ms) and `edge-tts` with `en-GB-RyanNeural` for speech
- Free to run — Groq free tier (30 req/min), edge-tts streams from Azure free tier
- Falls back to cached MP3s if the LLM call fails — never silent, never broken
- Works on any macOS Claude Code install

## Requirements

- macOS (uses `afplay`)
- Python 3.10+
- `jq`, `curl` (`brew install jq curl`)
- A free Groq API key — [console.groq.com/keys](https://console.groq.com/keys)

## Install

```bash
git clone https://github.com/filmy-munky/claude-code-jarvis.git
cd claude-code-jarvis
./install.sh
```

The installer copies the hook to `~/.claude/hooks/`, installs `edge-tts`, creates a `.env` template, and prints next steps.

Then:

1. Paste your Groq key into `~/.claude/hooks/.env`
2. Merge the `Stop` hook block from `settings.json.example` into `~/.claude/settings.json`
3. Open a new Claude Code session — after the first reply, you'll hear a chime and a spoken line

## Configure

Edit `~/.claude/hooks/axe-voice.sh`:

| Variable | Default | Notes |
|----------|---------|-------|
| `EDGE_VOICE` | `en-GB-RyanNeural` | Run `python3 -m edge_tts --list-voices` for alternatives |
| `GROQ_MODEL` | `llama-3.1-8b-instant` | Swap for `llama-3.3-70b-versatile` for smarter (slower) lines |

Edit `CLAUDE.md.example` → drop into `~/.claude/CLAUDE.md` to make Claude's written tone match the voice.

## Files

| Path | Purpose |
|------|---------|
| `hooks/axe-voice.sh` | The Stop hook — tails transcript, calls Groq, speaks via edge-tts |
| `hooks/.env.example` | Template for `GROQ_API_KEY` |
| `hooks/voice-cache/` | Fallback MP3s used if Groq errors |
| `settings.json.example` | Stop hook wiring for `~/.claude/settings.json` |
| `CLAUDE.md.example` | JARVIS persona prompt for the text layer |
| `install.sh` | One-shot installer |
| `assets/ad.html` | Self-contained Apple-ad animation (22s loop, screen-record ready) |

## Troubleshooting

Check `~/.claude/hooks/voice.log` — every invocation logs there.

- **Silent** — missing `GROQ_API_KEY`, `jq`, `edge-tts`, or wrong `PYTHON_BIN`
- **Voice spells "A dot X dot E dot"** — your persona file has punctuated name; use `AXE` as one word
- **Generic lines only** — Groq call is failing; check `voice.log` for HTTP errors

---

Built by [Punith Gowda](https://github.com/filmy-munky)
