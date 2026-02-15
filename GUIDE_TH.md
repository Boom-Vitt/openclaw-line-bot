# คู่มือติดตั้ง OpenClaw + LINE แบบสมบูรณ์

ติดตั้ง AI บอทที่คุยผ่าน LINE ได้ ใช้เวลาไม่เกิน 15 นาที

**ต้องการแค่ Docker เท่านั้น** — ใช้ได้ทั้ง VPS และเครื่องตัวเอง

---

## สารบัญ

1. [สิ่งที่ต้องเตรียม](#1-สิ่งที่ต้องเตรียม)
2. [ติดตั้ง Docker](#2-ติดตั้ง-docker)
3. [รันสคริปต์ติดตั้ง](#3-รันสคริปต์ติดตั้ง)
4. [สร้าง LINE Bot](#4-สร้าง-line-bot)
5. [สมัคร AI API](#5-สมัคร-ai-api)
6. [ใส่ credentials](#6-ใส่-credentials)
7. [เริ่มระบบ](#7-เริ่มระบบ)
8. [ตั้ง LINE Webhook](#8-ตั้ง-line-webhook)
9. [ทดสอบ](#9-ทดสอบ)
10. [แก้ไขปัญหา](#10-แก้ไขปัญหา)
11. [คำสั่งที่ใช้บ่อย](#11-คำสั่งที่ใช้บ่อย)

---

## 1. สิ่งที่ต้องเตรียม

- **Docker** — [ติดตั้งที่นี่](https://docs.docker.com/get-docker/)
- **LINE account** — สำหรับสร้างบอท
- **AI API key** — เลือกอย่างน้อย 1 อย่าง (ดูขั้นตอนที่ 5)

### ถ้าใช้ VPS (เซิร์ฟเวอร์)

- โดเมนที่ชี้มายัง IP เซิร์ฟเวอร์แล้ว
- เปิด port 80 และ 443

### ถ้าใช้เครื่องตัวเอง (Local)

- ไม่ต้องมีโดเมน
- เข้าผ่าน `localhost:18789`

---

## 2. ติดตั้ง Docker

### Linux (Ubuntu/Debian)

```bash
curl -fsSL https://get.docker.com | sh
```

### macOS

ดาวน์โหลด [Docker Desktop](https://www.docker.com/products/docker-desktop/)

### Windows

ดาวน์โหลด [Docker Desktop](https://www.docker.com/products/docker-desktop/) แล้วเปิด WSL

ตรวจสอบว่าทำงานแล้ว:

```bash
docker --version
```

---

## 3. รันสคริปต์ติดตั้ง

```bash
git clone https://github.com/Boom-Vitt/openclaw-line-bot.git
cd openclaw-line-bot
bash setup-openclaw.sh
```

สคริปต์จะถาม:

```
จะติดตั้งที่ไหน?

  1) VPS   — เซิร์ฟเวอร์ที่มีโดเมน (ติดตั้ง Traefik + SSL อัตโนมัติ)
  2) Local — เครื่องตัวเอง, เข้าผ่าน localhost เท่านั้น
```

#### ถ้าเลือก VPS

สคริปต์จะถามเพิ่ม:
- **โดเมน** — เช่น `srv1068766.hstgr.cloud`
- **อีเมล SSL** — สำหรับ Let's Encrypt
- **Timezone** — เช่น `Asia/Bangkok`

ระบบจะสร้าง Traefik + SSL อัตโนมัติ ได้ URL: `https://openclaw.โดเมนของคุณ`

#### ถ้าเลือก Local

ได้ URL: `http://localhost:18789` — ไม่ต้องตั้งค่าอะไรเพิ่ม

---

## 4. สร้าง LINE Bot

### 4.1 เข้า LINE Developers Console

1. ไปที่ https://developers.line.biz/console/
2. เข้าสู่ระบบด้วย LINE Account ของคุณ

### 4.2 สร้าง Provider

1. คลิก **"Create"** ที่หน้า Provider
2. ตั้งชื่อ เช่น `MyBot`
3. คลิก **"Create"**

### 4.3 สร้าง Channel

1. คลิก **"Create a Messaging API channel"**
2. กรอกข้อมูล:
   - **Channel name**: ชื่อบอทที่จะแสดงใน LINE
   - **Channel description**: รายละเอียดสั้นๆ
   - **Category**: เลือก `ความบันเทิง` หรือ `อื่นๆ`
   - **Subcategory**: เลือก `Chatbot`
3. ยอมรับข้อกำหนด → คลิก **"Create"**

### 4.4 เอา Token และ Secret

เข้าไปที่ channel ที่สร้าง:

**Channel Secret:**
- อยู่ในแท็บ **"Basic settings"**
- คัดลอกค่า **Channel secret**

**Channel Access Token:**
- ไปที่แท็บ **"Messaging API"**
- เลื่อนลงหา **"Channel access token"**
- คลิก **"Issue"** (ถ้ายังไม่มี)
- คัดลอก token ที่ได้

> เก็บ 2 ค่านี้ไว้ — จะใช้ในขั้นตอนที่ 6

### 4.5 ปิด Auto-reply

ยังอยู่ในแท็บ **"Messaging API"**:
- คลิก **"Edit"** ที่ Auto-reply messages
- **ปิด** "Auto-reply messages"
- **ปิด** "Greeting messages"

ถ้าไม่ปิด บอทจะส่งข้อความตอบกลับอัตโนมัติของ LINE แทนที่จะเป็น AI

---

## 5. สมัคร AI API

เลือกอย่างน้อย 1 อย่าง — แนะนำเริ่มจาก OpenRouter เพราะง่ายที่สุด

### ตัวเลือก 1: OpenRouter (แนะนำ — มี quota ฟรี)

1. ไปที่ https://openrouter.ai/
2. สมัครสมาชิก
3. ไปที่ **Keys** → **Create Key**
4. คัดลอก API key (ขึ้นต้นด้วย `sk-or-...`)

ใช้ model: `openrouter/google/gemini-2.0-flash` (ฟรี)

### ตัวเลือก 2: Anthropic (Claude)

1. ไปที่ https://console.anthropic.com/
2. สมัครและเติมเงิน
3. สร้าง API key

ใช้ model: `anthropic/claude-sonnet-4-5`

### ตัวเลือก 3: Kimi Coding

1. ไปที่ https://kimi.ai/
2. สมัครและได้ API key

ใช้ model: `kimi-coding/k2p5`

---

## 6. ใส่ credentials

แก้ไขไฟล์ `~/.openclaw/openclaw.json`:

```bash
nano ~/.openclaw/openclaw.json
```

### สิ่งที่ต้องแก้:

#### ใส่ AI model

หา `"YOUR_PROVIDER/YOUR_MODEL"` แล้วเปลี่ยนเป็น model ที่เลือก เช่น:

```json
"primary": "openrouter/google/gemini-2.0-flash"
```

#### ใส่ LINE credentials

หา `channels.line` แล้วเปลี่ยน:

```json
"line": {
  "channelAccessToken": "ใส่ token ที่คัดลอกจากขั้นตอน 4.4",
  "channelSecret": "ใส่ secret ที่คัดลอกจากขั้นตอน 4.4",
  "enabled": true,
  "webhookPath": "/webhook/line",
  "dmPolicy": "pairing"
}
```

> สำคัญ: เปลี่ยน `"enabled": false` เป็น `"enabled": true`

#### ใส่ LINE ใน plugins ด้วย

หา `plugins.entries.line` แล้วเปลี่ยน:

```json
"line": { "enabled": true }
```

บันทึกไฟล์ (ใน nano กด `Ctrl+O` → `Enter` → `Ctrl+X`)

---

## 7. เริ่มระบบ

```bash
cd ~/openclaw-setup
docker compose up -d --build
```

รอสักครู่ ครั้งแรกจะ build Docker image (ใช้เวลาสัก 2-3 นาที)

ตรวจสอบว่าทำงานแล้ว:

```bash
docker compose logs -f openclaw
```

ถ้าเห็น `Gateway listening on 0.0.0.0:18789` แปลว่าสำเร็จ

กด `Ctrl+C` เพื่อออกจาก logs

---

## 8. ตั้ง LINE Webhook

กลับไปที่ LINE Developers Console:

1. เข้าไปที่ channel ของคุณ
2. ไปแท็บ **"Messaging API"**
3. หา **"Webhook URL"** → คลิก **"Edit"**

### ถ้าใช้ VPS

ใส่ URL:
```
https://openclaw.โดเมนของคุณ/webhook/line
```

ตัวอย่าง: `https://openclaw.srv1068766.hstgr.cloud/webhook/line`

> ต้องชี้ DNS ของ `openclaw.โดเมนของคุณ` มายัง IP เซิร์ฟเวอร์ก่อน
>
> วิธีตั้ง DNS: เข้า panel ของผู้ให้บริการโดเมน → เพิ่ม A record:
> - **Name**: `openclaw`
> - **Type**: `A`
> - **Value**: IP เซิร์ฟเวอร์ของคุณ

### ถ้าใช้ Local

LINE ต้องการ HTTPS จึงต้องใช้ tunnel เช่น:

**วิธี ngrok (ง่ายที่สุด สำหรับทดสอบ):**
```bash
# ติดตั้ง ngrok: https://ngrok.com/download
ngrok http 18789
```

จะได้ URL เช่น `https://xxxx.ngrok-free.app` — ใส่ URL นี้ตามด้วย `/webhook/line`:
```
https://xxxx.ngrok-free.app/webhook/line
```

4. เปิด **"Use webhook"** → **On**
5. คลิก **"Verify"** — ต้องขึ้น **"Success"**

---

## 9. ทดสอบ

### 9.1 เพิ่มบอทเป็นเพื่อน

1. ในหน้า channel → แท็บ **"Messaging API"**
2. จะเห็น **QR code** ของบอท
3. สแกนด้วย LINE เพื่อเพิ่มเป็นเพื่อน

### 9.2 ส่งข้อความทดสอบ

1. เปิดแชทกับบอทใน LINE
2. พิมพ์ข้อความอะไรก็ได้ เช่น `สวัสดี`
3. บอทจะส่งรหัสจับคู่ (pairing code) กลับมา

### 9.3 อนุมัติการจับคู่

ดูรหัสจับคู่ใน logs:

```bash
docker compose logs openclaw | grep -i pairing
```

อนุมัติ:

```bash
docker compose exec openclaw openclaw pairing approve line <รหัสที่ได้>
```

### 9.4 พร้อมใช้งาน

ส่งข้อความอีกครั้ง — AI จะตอบกลับแล้ว

---

## 10. แก้ไขปัญหา

### Gateway ไม่เริ่ม

```bash
# ดู logs
docker compose logs openclaw

# ตรวจสอบว่า container ทำงาน
docker compose ps
```

### Webhook Verify ไม่ผ่าน

- ตรวจสอบว่า **Channel Secret** ตรงกับที่ใส่ใน config
- URL ต้องเป็น **HTTPS** (VPS ใช้ Traefik จัดการ, Local ใช้ ngrok)
- ตรวจสอบว่า DNS ชี้มาถูก IP (VPS)

### ส่งข้อความแล้วบอทไม่ตอบ

1. ตรวจสอบว่าปิด **Auto-reply messages** แล้ว (ขั้นตอน 4.5)
2. ตรวจสอบว่า `"enabled": true` ทั้งใน `channels.line` และ `plugins.entries.line`
3. ตรวจสอบ logs:
   ```bash
   docker compose logs -f openclaw | grep -i line
   ```
4. ถ้า `dmPolicy` เป็น `"pairing"` → ต้องอนุมัติก่อน (ขั้นตอน 9.3)

### Channel Access Token หมดอายุ

1. ไปที่ LINE Console → แท็บ "Messaging API"
2. คลิก **"Reissue"** ที่ Channel access token
3. คัดลอก token ใหม่ใส่ใน `openclaw.json`
4. Restart:
   ```bash
   docker compose restart openclaw
   ```

### Port ถูกใช้งานอยู่

```bash
# ตรวจสอบว่ามีอะไรใช้ port 18789
lsof -i :18789

# เปลี่ยน port ในไฟล์ .env
# OPENCLAW_PORT=18790
```

---

## 11. คำสั่งที่ใช้บ่อย

```bash
# เริ่มระบบ
docker compose up -d --build

# หยุดระบบ
docker compose down

# ดู logs แบบ realtime
docker compose logs -f openclaw

# restart
docker compose restart openclaw

# เข้าไปใน container
docker compose exec openclaw sh

# ใช้ wizard ตั้งค่า
docker compose run --rm openclaw configure

# ดูสถานะ plugin
docker compose exec openclaw openclaw plugins list

# ดูรายการจับคู่รอ
docker compose exec openclaw openclaw pairing list line

# อนุมัติจับคู่
docker compose exec openclaw openclaw pairing approve line <CODE>

# ส่งข้อความทดสอบ
docker compose exec openclaw openclaw message send --channel line --target "Uxxxxx" "ทดสอบ"

# อัพเดท OpenClaw (build ใหม่)
docker compose build --no-cache && docker compose up -d
```

---

## Checklist

- [ ] ติดตั้ง Docker แล้ว
- [ ] รัน `setup-openclaw.sh` แล้ว
- [ ] สร้าง LINE Bot ที่ LINE Developers Console แล้ว
- [ ] ได้ Channel Access Token และ Channel Secret แล้ว
- [ ] สมัคร AI API แล้ว (OpenRouter/Anthropic/อื่นๆ)
- [ ] แก้ไข `~/.openclaw/openclaw.json` แล้ว (ใส่ token + เปิด enabled)
- [ ] รัน `docker compose up -d --build` แล้ว
- [ ] ตั้ง Webhook URL ใน LINE Console แล้ว
- [ ] Verify webhook สำเร็จ
- [ ] เพิ่มบอทเป็นเพื่อนใน LINE แล้ว
- [ ] อนุมัติการจับคู่แล้ว
- [ ] ส่งข้อความทดสอบและ AI ตอบกลับแล้ว
