# OpenClaw LINE Bot Setup

ติดตั้ง [OpenClaw](https://docs.openclaw.ai/) AI Bot สำหรับ LINE ด้วย Docker — ใช้ได้ทั้ง VPS และเครื่องตัวเอง

**ต้องการแค่ Docker เท่านั้น**

> **คู่มือแบบละเอียดทุกขั้นตอน** → [GUIDE_TH.md](GUIDE_TH.md)

## เริ่มต้นใช้งาน

```bash
git clone https://github.com/Boom-Vitt/openclaw-line-bot.git
cd openclaw-line-bot
bash setup-openclaw.sh
```

สคริปต์จะถามว่าจะติดตั้งที่ไหน:

- **VPS** → Traefik + SSL อัตโนมัติ → `https://openclaw.โดเมนของคุณ`
- **Local** → `http://localhost:18789`

จากนั้น:

1. แก้ไข `~/.openclaw/openclaw.json` — ใส่ API key + LINE token
2. `docker compose up -d --build`
3. ตั้ง LINE webhook → `https://openclaw.โดเมนของคุณ/webhook/line`

## สิ่งที่มีให้

- Dockerfile (Node 22 + OpenClaw + LINE plugin ในตัว)
- docker-compose.yml (VPS: มี Traefik + SSL อัตโนมัติ)
- ไฟล์ workspace template สำหรับ AI agent
- สร้าง gateway auth token ให้อัตโนมัติ

## สิ่งที่ต้องเตรียมเอง

- AI model provider + API key (เช่น OpenRouter, Anthropic, Kimi)
- LINE channel access token & secret (จาก [LINE Developers Console](https://developers.line.biz/))
- Telegram bot token (ถ้าต้องการ)

## คำสั่งที่ใช้บ่อย

```bash
docker compose up -d --build          # เริ่มระบบ
docker compose logs -f openclaw       # ดู logs
docker compose run --rm openclaw configure  # ตั้งค่าผ่าน wizard
docker compose down                   # หยุดระบบ
docker compose build --no-cache && docker compose up -d  # อัพเดท OpenClaw
```
