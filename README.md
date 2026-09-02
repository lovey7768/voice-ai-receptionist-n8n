# voice-ai-receptionist-n8n
# 🎙️ Autonomous Voice AI Receptionist & Appointment Engine

[![Docker](https://img.shields.io/badge/Container-Docker-2496ED.svg)](https://www.docker.com/)
[![Workflow Engine](https://img.shields.io/badge/Workflow-n8n-FF6D5A.svg)](https://n8n.io/)
[![Voice Telephony](https://img.shields.io/badge/Voice-Vapi.ai-7B2CBF.svg)](https://vapi.ai/)
[![Inference](https://img.shields.io/badge/Inference-Groq_LPU-00B4D8.svg)](https://groq.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

A production-ready, low-latency conversational Voice AI receptionist and appointment engine designed for professional services (healthcare, dental, legal, real estate). It combines Vapi.ai's voice pipeline with a deterministic n8n workflow to reliably book calendar events without hallucinated dates or malformed payloads.

---

## ⚡ Engineering Highlights

- Sub-700ms audio latency: Deepgram Nova-2 for STT, Cartesia for natural TTS and Groq LPU for efficient LLM inference to keep conversations fluid and human-like.
- Deterministic Tool Calling: Strict JSON schemas for the book_appointment tool ensure structured payloads and prevent hallucinated booking details.
- Containerized Orchestration: Runs on Docker with persistent volumes and restart policies for production reliability.
- Bi-directional Webhook Handshake: Vapi waits for n8n to acknowledge a successful calendar insertion before confirming verbally to the caller.


## 🛠️ Tech Stack

- Voice & Telephony: Vapi.ai (Deepgram Nova-2 STT + Cartesia TTS)
- Orchestration: n8n (self-hosted, Docker)
- Edge Tunnel: Cloudflare Tunnel (cloudflared)
- Inference: Groq LPU (llama-3.3-70b-versatile / openai/gpt-oss-120b)
- Calendar & CRM: Google Calendar API


## 🔧 Repository Structure

```
voice-ai-receptionist-n8n/
├── workflows/
│   └── vapi-calendar-booking.json   # Exported n8n workflow JSON
├── docker-compose.yml              # Production container config
├── README.md                       # Architecture & portfolio documentation
├── .gitignore
└── prompts/
    └── system-prompt.txt           # Vapi agent system prompt & schemas
```


## 🏛️ System Architecture

```text
[Incoming Voice Call / WebRTC]
              │
              ▼
    ┌──────────────────┐
    │     Vapi.ai      │ ◄── Deepgram Nova-2 (STT) + Cartesia (TTS)
    │  Voice Pipeline  │ ◄── Groq LPU LLM Reasoning Engine
    └─────────┬────────┘
              │ (Emits Tool Call: book_appointment)
              ▼
    ┌──────────────────┐
    │ Cloudflare Tunnel│ (Secure Edge Proxy)
    └─────────┬────────┘
              │ (POST Webhook)
              ▼
    ┌──────────────────┐
    │ Dockerized n8n   │
    │ Workflow Engine  │
    └─────────┬────────┘
              │
      ┌───────┴───────────────────────┐
      ▼                               ▼
┌──────────────┐             ┌─────────────────┐
│ Parse Dates  │             │ Google Calendar │ ──► Event Created
│ & Payloads   │             │ API Integration │
└──────────────┘             └─────────────────┘
```


## 🚀 Quickstart — Run locally (production-ish)

1. Start n8n in Docker

```bash
docker run -d --name n8n -p 5678:5678 -v n8n_data:/home/node/.n8n --restart unless-stopped docker.n8n.io/n8nio/n8n
```

2. Expose n8n to the internet (Cloudflare Tunnel)

```bash
npx cloudflared tunnel --url http://localhost:5678
```

3. Import the workflow

- Open http://localhost:5678 in your browser (or your tunnel URL)
- Import workflows/vapi-calendar-booking.json
- Connect your Google Calendar credentials in the workflow
- Activate the workflow

4. Configure the Vapi Assistant

- Use prompts/system-prompt.txt as the assistant/system prompt.
- Point the book_appointment tool server URL to:

```
https://<your-tunnel-url>/webhook/voice-booking
```

5. Test an incoming call via Vapi.ai and confirm that the webhook handshake creates a Google Calendar event and returns an affirmative response to Vapi before TTS confirmation.


## 📄 Prompts & Schemas

- prompts/system-prompt.txt — contains the Vapi system prompt and the strict JSON schema used for tool calling (book_appointment). Keep this file authoritative; workflows rely on its schema for safe, deterministic calls.


## ✅ What I shipped (Highlights for recruiters)

- A complete, end-to-end voice-to-calendar pipeline using Vapi.ai and n8n.
- Deterministic tool schema to remove ambiguity from voice bookings.
- Simple Docker-first deployment and Cloudflare Tunnel recipe to demo publicly.
- Ready-to-import n8n workflow: workflows/vapi-calendar-booking.json


## 📞 Recruiters — Canadian opportunities

Hi — if you're reviewing this for hiring: I'm the author of this repo (GitHub: @lovey7768). I'm actively interviewing and open to opportunities in Canada. If you'd like a live demo or an interview today, please contact me via GitHub or open an issue and I will reply promptly.


## 🧪 Notes, Caveats & Next steps

- You must provision Vapi.ai credentials and Groq access separately — keys are not included in this repo.
- For production deployments, run n8n behind a process manager or orchestrator and secure your webhooks with signatures and HTTPS.
- Consider adding automated tests (integration for webhook handshakes) and a small demo script to simulate calls for easier recruiter demos.


## 🪪 License

MIT License

---

### Step 6: Initialize Git and Push to GitHub

In your PC terminal, navigate into your folder:

```bash
cd ~/voice-ai-receptionist-n8n  # or your directory path

git init -b main
git add .
git commit -m "feat: complete voice AI receptionist with Vapi, Dockerized n8n, and Google Calendar integration"

# Replace with your GitHub repo URL
git remote add origin https://github.com/YOUR_USERNAME/voice-ai-receptionist-n8n.git
git push -u origin main
```
