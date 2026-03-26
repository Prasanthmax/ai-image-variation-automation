# 🎨 AI Image Variation Automation

<div align="center">

![Banner](docs/images/banner.png)

**Send any image to a Telegram bot. Get back an AI-generated artistic variation. Fully automated. 100% free.**

[![n8n](https://img.shields.io/badge/n8n-v2.11.3-orange?style=for-the-badge&logo=n8n)](https://n8n.io)
[![Docker](https://img.shields.io/badge/Docker-Ready-2496ED?style=for-the-badge&logo=docker)](https://docker.com)
[![FLUX](https://img.shields.io/badge/FLUX.1-schnell-blueviolet?style=for-the-badge)](https://siliconflow.cn)
[![Telegram](https://img.shields.io/badge/Telegram-Bot-26A5E4?style=for-the-badge&logo=telegram)](https://telegram.org)
[![License: MIT](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)](LICENSE)

[🚀 Quick Start](#-quick-start) • [🏗 Architecture](#-architecture) • [🔧 Node Reference](#-workflow---node-by-node) • [📸 Screenshots](#-screenshots) • [🤝 Contributing](#-contributing)

</div>

---

## ✨ What Is This?

This project is a **fully automated AI image variation pipeline** built on top of n8n, triggered entirely through Telegram. No frontend. No manual steps. Just send a photo and a description — and your AI-generated variation lands right back in your chat.

> 💡 Built as a personal automation project by **Prasanth** for learning purpose — March 2026.

---

## 🎬 How It Works

```
You (Telegram)
     │
     │  📸 image + "make it look like a watercolor painting"
     ▼
┌─────────────────────────────────────────────────────────┐
│                     n8n Workflow                        │
│                                                         │
│  📥 Receive  →  🔍 Analyse  →  ✍️ Prompt  →  🎨 Generate │
│                                                         │
│  Telegram       Mistral        Groq           FLUX      │
│  Poller         Pixtral        LLaMA          schnell   │
└─────────────────────────────────────────────────────────┘
     │
     │  🖼️ AI-generated variation + caption
     ▼
You (Telegram)
```

**In plain English:**
1. 📤 You send any image + a style description to the Telegram bot
2. 🔍 Mistral Pixtral *sees* your image and describes it in detail
3. ✍️ Groq LLaMA combines your description + the image analysis into a precise FLUX prompt
4. 🎨 SiliconFlow FLUX.1-schnell generates a brand-new AI variation
5. 💾 The image is stored permanently in MinIO (self-hosted S3)
6. 📝 Groq writes a natural, human-sounding caption grounded to the image
7. 📬 The bot sends the result back to you on Telegram

---

## 🏗 Architecture

```
┌──────────────────────────────────────────────────────────────────┐
│                        Your Machine                              │
│                                                                  │
│   ┌─────────────┐        ┌──────────────────────────────────┐   │
│   │   Docker    │        │           n8n Workflow           │   │
│   │             │        │  ┌────────┐      ┌────────────┐  │   │
│   │  ┌───────┐  │        │  │Schedule│─────▶│  Telegram  │  │   │
│   │  │  n8n  │  │        │  │Trigger │      │   Poller   │  │   │
│   │  │ :5678 │  │        │  └────────┘      └─────┬──────┘  │   │
│   │  └───────┘  │        │                        │         │   │
│   │             │        │               ┌─────────▼──────┐ │   │
│   │  ┌───────┐  │        │               │ Process Input  │ │   │
│   │  │ MinIO │  │        │               │ (extract photo,│ │   │
│   │  │ :9000 │  │        │               │  chatId, desc) │ │   │
│   │  └───────┘  │        │               └─────────┬──────┘ │   │
│   └─────────────┘        │                         │        │   │
│                           │          ┌──────────────▼─────┐ │   │
│                           │          │  Mistral Pixtral   │ │   │
└──────────────────────────┘          │  (image analysis)  │ │   │
                                       └──────────────┬─────┘ │   │
              External APIs                           │        │   │
         ┌────────────────────┐        ┌──────────────▼─────┐ │   │
         │  Mistral AI        │        │   Groq LLaMA        │ │   │
         │  Groq              │        │   (build FLUX       │ │   │
         │  SiliconFlow FLUX  │        │    prompt)          │ │   │
         │  Telegram Bot API  │        └──────────────┬─────┘ │   │
         └────────────────────┘                       │        │   │
                                        ┌─────────────▼─────┐ │   │
                                        │  FLUX.1-schnell   │ │   │
                                        │  (generate image) │ │   │
                                        └─────────────┬─────┘ │   │
                                                       │        │   │
                                        ┌─────────────▼─────┐ │   │
                                        │  MinIO Storage    │ │   │
                                        │  (save variant)   │ │   │
                                        └─────────────┬─────┘ │   │
                                                       │        │   │
                                        ┌─────────────▼─────┐ │   │
                                        │  Groq LLaMA       │ │   │
                                        │  (write caption)  │ │   │
                                        └─────────────┬─────┘ │   │
                                                       │        │   │
                                        ┌─────────────▼─────┐ │   │
                                        │  Telegram Bot     │ │   │
                                        │  (send result)    │ │   │
                                        └───────────────────┘ │   │
                                                               │   │
                                       └──────────────────────┘   │
└──────────────────────────────────────────────────────────────────┘
```

---

## 🛠 Tech Stack — All Free

| Service | Purpose | Model / Tool | Cost |
|---|---|---|---|
| **n8n** (self-hosted) | Workflow automation engine | v2.11.3 | Free |
| **Docker Desktop** | Container runtime | v29.1.3 | Free |
| **MinIO** | Image storage (self-hosted S3) | Latest | Free |
| **Mistral AI** | Image analysis (vision) | pixtral-12b-2409 | Free tier |
| **Groq** | Prompt + caption generation | llama-3.3-70b-versatile | Free tier |
| **SiliconFlow** | AI image generation | FLUX.1-schnell | Free credits |
| **Telegram** | Input + output channel | Bot API | Free |

---

## 📸 Screenshots

<div align="center">
  <table border="0">
    <tr>
      <th align="center">n8n Workflow Canvas</th>
      <th align="center">Telegram Bot Demo</th>
      <th align="center">MinIO Storage</th>
    </tr>
    <tr>
      <td>
        <img width="400" alt="Workflow" src="https://github.com/user-attachments/assets/5a21e4ec-bb68-463e-b9cf-668ecd74a8aa" />
      </td>
      <td>
        <img width="400" alt="Bot" src="https://github.com/user-attachments/assets/3a12c1cc-17da-4b1e-a3b6-bf9e44a61bf7" />
      </td>
      <td>
        <img width="400" alt="MinIO" src="https://github.com/user-attachments/assets/ec9bc124-03e9-4b6e-a9f9-d40e33abdba6" />
      </td>
    </tr>
  </table>
</div>


---

## 🚀 Quick Start

### Prerequisites

- Windows 11 + WSL2 or any Linux system
- [Docker Desktop](https://www.docker.com/products/docker-desktop/) installed
- [n8n](https://n8n.io) running via Docker
- Free accounts on: Mistral AI, Groq, SiliconFlow
- A Telegram bot created via [@BotFather](https://t.me/botfather)

---

### 1. Clone the repo

```bash
git clone https://github.com/prasanthmax/ai-image-variation-automation.git
cd ai-image-variation-automation
```

### 2. Set up your environment

```bash
cp .env.example .env
```

Open `.env` and fill in your actual API keys:

```env
TELEGRAM_BOT_TOKEN=your_token_here
MISTRAL_API_KEY=your_key_here
GROQ_API_KEY=your_key_here
SILICONFLOW_API_KEY=your_key_here
MINIO_ROOT_USER=adminuser
MINIO_ROOT_PASSWORD=your_password_here
```

### 3. Start Docker services

```bash
docker compose up -d
```

This starts:
- **n8n** at `http://localhost:5678`
- **MinIO** console at `http://localhost:9001`

### 4. Create MinIO buckets

1. Open `http://localhost:9001`
2. Log in with your MinIO credentials
3. Create two buckets: `base-images` and `variants`

### 5. Import the workflow

1. Open `http://localhost:5678`
2. Go to **Workflows → Import from file**
3. Select `workflow.json` from this repo
4. Add your credentials inside each node that requires them

### 6. Activate the workflow

Toggle the workflow to **Active** in n8n — that's it! 🎉

Now send a photo + description to your Telegram bot and watch the magic happen.

---

## 🔧 Workflow — Node by Node

The pipeline has **18 nodes** running in sequence:

| # | Node | Type | Purpose |
|---|---|---|---|
| 1 | Schedule Trigger | Trigger | Polls every 10 seconds |
| 2 | Telegram Poller | HTTP Request | Fetches latest message via getUpdates |
| 3 | Process Input | Code (JS) | Extracts photo, chatId, description — skips if no photo |
| 4 | Get Telegram File | HTTP Request | Resolves file_id to a file path |
| 5 | Build Image URL | Code (JS) | Builds full HTTPS download URL |
| 6 | Image Analyser | HTTP Request | Mistral Pixtral describes the image |
| 7 | Build Prompt Data | Code (JS) | Combines image analysis + user description |
| 8 | Build Groq Body | Code (JS) | Prepares request body for prompt generation |
| 9 | Prompt Generator | HTTP Request | Groq LLaMA creates a detailed FLUX prompt |
| 10 | Extract Prompt | Code (JS) | Parses and extracts the prompt string |
| 11 | FLUX Image Generator | HTTP Request | SiliconFlow generates the AI image |
| 12 | Extract Image URL | Code (JS) | Gets the generated image URL |
| 13 | Download Image | HTTP Request | Downloads binary image data |
| 14 | Upload to MinIO | HTTP Request | Saves image to MinIO `variants` bucket |
| 15 | Build Caption Body | Code (JS) | Prepares Groq request for caption |
| 16 | Caption Generator | HTTP Request | Groq writes a natural caption |
| 17 | Extract Caption | Code (JS) | Parses caption from Groq response |
| 18 | Send to Telegram | HTTP Request | Sends image + caption back to the user |

---

## 🔑 API Keys — Where to Get Them

| Service | Sign Up | Free Tier |
|---|---|---|
| Telegram Bot | [@BotFather](https://t.me/botfather) on Telegram | ✅ Always free |
| Mistral AI | [console.mistral.ai](https://console.mistral.ai) | ✅ Free tier available |
| Groq | [console.groq.com](https://console.groq.com) | ✅ Generous free tier |
| SiliconFlow | [cloud.siliconflow.cn](https://cloud.siliconflow.cn) | ✅ Free credits on signup |

---

## 📁 Project Structure

```
ai-image-variation-automation/
│
├── docker-compose.yml       # Spins up n8n + MinIO
├── workflow.json            # Full n8n workflow (secrets scrubbed)
├── .env.example             # Template for your API keys
├── .gitignore               # Excludes data folders and .env
└── README.md                # You are here 👋
```

---

## 🗺 Roadmap

- [x] Phase 1 — Telegram bot receiving images
- [x] Phase 2 — Mistral vision image analysis
- [x] Phase 3 — Groq FLUX prompt generation
- [x] Phase 4 — FLUX image generation via SiliconFlow
- [x] Phase 5 — Caption generation + Telegram delivery
- [ ] Phase 6 — Supabase engagement feedback loop
- [ ] Phase 7 — Style selection menu in Telegram
- [ ] Phase 8 — Multi-image batch processing

---

## 🤝 Contributing

Got ideas to make this better? PRs are welcome!

1. Fork the repo
2. Create your branch: `git checkout -b feature/your-idea`
3. Commit your changes: `git commit -m "Add your idea"`
4. Push to the branch: `git push origin feature/your-idea`
5. Open a Pull Request

---

## 👨‍💻 Author

**Prasanth** — [@prasanthmax](https://github.com/prasanthmax)

Built with curiosity and a cup of coffee ☕🤖

---

## 📄 License

This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.

---

<div align="center">

**If this project helped you or inspired you, drop a ⭐ — it means a lot!**

Made by Prasanth | Powered by n8n + FLUX + Telegram

</div>
