# OpenClaw Docker Setup

One-script setup for [OpenClaw](https://docs.openclaw.ai/) — AI gateway with LINE, Telegram, and more.

**Only requirement: Docker.** Works on VPS or local machine.

> **คู่มือภาษาไทยแบบละเอียด** → [GUIDE_TH.md](GUIDE_TH.md)

## Quick start

```bash
git clone https://github.com/Boom-Vitt/openclaw-setup.git
cd openclaw-setup
bash setup-openclaw.sh
```

The script asks where you're deploying:

- **VPS** → Traefik + auto SSL → `https://openclaw.yourdomain.com`
- **Local** → `localhost:18789`

Then:

1. Edit `~/.openclaw/openclaw.json` — add API keys + LINE tokens
2. `docker compose up -d --build`
3. Set LINE webhook → `https://openclaw.yourdomain.com/webhook/line`

## What's included

- Dockerfile (Node 22 + OpenClaw + LINE plugin built-in)
- docker-compose.yml (VPS: + Traefik with auto SSL)
- Workspace templates
- Auto-generated gateway auth token

## What you provide

- AI model provider + API key
- LINE channel access token & secret (from [LINE Developers Console](https://developers.line.biz/))
- Telegram bot token (optional)

## Commands

```bash
docker compose up -d --build          # Start
docker compose logs -f openclaw       # Logs
docker compose run --rm openclaw configure  # Wizard
docker compose down                   # Stop
docker compose build --no-cache && docker compose up -d  # Update
```
