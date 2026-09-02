# 🎙️ Autonomous Voice AI Receptionist & Appointment Engine

[![Docker](https://img.shields.io/badge/Container-Docker-2496ED.svg)](https://www.docker.com/)
[![Workflow Engine](https://img.shields.io/badge/Workflow-n8n-FF6D5A.svg)](https://n8n.io/)
[![Voice Telephony](https://img.shields.io/badge/Voice-Vapi.ai-7B2CBF.svg)](https://vapi.ai/)
[![Inference](https://img.shields.io/badge/Inference-Groq_LPU-00B4D8.svg)](https://groq.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

A production-ready, low-latency conversational Voice AI receptionist and appointment engine designed for professional services (healthcare, dental, legal, real estate). It combines Vapi.ai's voice orchestration, n8n's workflow automation, and Groq's edge inference to handle appointment scheduling autonomously with sub-700ms audio latency.

## Why This Project?

Modern service businesses waste resources on phone-based appointment scheduling. Receptionists handle repetitive calls, time zone confusion, and double bookings. This system eliminates that overhead by:

- **Reducing administrative burden**: Fully autonomous inbound call handling, 24/7.
- **Keeping interactions natural**: Sub-700ms latency and voice continuity make the experience feel like talking to a real person.
- **Ensuring data integrity**: Deterministic tool schemas prevent booking errors and hallucinated details.
- **Staying developer-friendly**: n8n's visual workflow editor and Docker-first deployment make customization trivial.

---

## ⚡ Engineering Highlights

- **Sub-700ms audio latency**: Deepgram Nova-2 for STT, Cartesia for natural TTS, and Groq LPU for efficient LLM inference keep conversations fluid and human-like.
- **Deterministic Tool Calling**: Strict JSON schemas for the `book_appointment` tool ensure structured payloads and prevent hallucinated booking details.
- **Containerized Orchestration**: Runs on Docker with persistent volumes and restart policies for production reliability.
- **Bi-directional Webhook Handshake**: Vapi waits for n8n to acknowledge a successful calendar insertion before confirming verbally to the caller.

---

## 🛠️ Tech Stack

| Component | Technology | Purpose |
|-----------|-----------|---------|
| **Voice & Telephony** | Vapi.ai | Call orchestration, STT/TTS pipeline |
| **Speech-to-Text** | Deepgram Nova-2 | Fast, accurate transcription |
| **Text-to-Speech** | Cartesia | Natural voice synthesis |
| **LLM Inference** | Groq LPU | Sub-100ms latency reasoning |
| **Workflow Orchestration** | n8n (Docker) | Calendar & event handling |
| **Edge Tunnel** | Cloudflare Tunnel | Secure public webhook exposure |
| **Calendar & CRM** | Google Calendar API | Persistent event storage |

---

## 📁 Repository Structure

```
voice-ai-receptionist-n8n/
├── workflows/
│   └── vapi-calendar-booking.json   # Exported n8n workflow JSON
├── docker-compose.yml               # Production container config
├── README.md                        # Architecture & documentation
├── .gitignore
└── prompts/
    └── system-prompt.txt            # Vapi agent system prompt & JSON schemas
```

---

## 🏛️ System Architecture

```
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
┌──────────────┐         ┌─────────────────┐
│ Parse Dates  │         │ Google Calendar │ ──► Event Created
│ & Payloads   │         │ API Integration │
└──────────────┘         └─────────────────┘
```

---

## 🚀 Quickstart — Run Locally

### 1. Start n8n in Docker

```bash
docker run -d --name n8n -p 5678:5678 \
  -v n8n_data:/home/node/.n8n \
  --restart unless-stopped \
  docker.n8n.io/n8nio/n8n
```

### 2. Expose n8n to the internet (Cloudflare Tunnel)

```bash
npx cloudflared tunnel --url http://localhost:5678
```

Copy the tunnel URL — you'll need it in step 4.

### 3. Import the workflow

- Open `http://localhost:5678` in your browser
- Navigate to **Workflows** → **Import from file**
- Select `workflows/vapi-calendar-booking.json`
- Connect your Google Calendar credentials in the workflow
- Activate the workflow

### 4. Configure the Vapi Assistant

- Log into [Vapi.ai](https://vapi.ai)
- Create or update your assistant with `prompts/system-prompt.txt`
- Set the `book_appointment` tool server URL to:

```
https://<your-tunnel-url>/webhook/voice-booking
```

- Test with an incoming call to verify the webhook handshake creates a Google Calendar event

---

## 📄 Prompts & Schemas

See `prompts/system-prompt.txt` for:
- Full Vapi system prompt (instructions for conversation tone, handling edge cases)
- Strict JSON schema for `book_appointment` tool calling
- Example payloads

**Keep this file authoritative** — workflows depend on its schema for deterministic parsing.

---

## ✅ What's Included

- ✓ End-to-end voice-to-calendar pipeline using Vapi.ai and n8n
- ✓ Deterministic tool schema to remove ambiguity from voice bookings
- ✓ Docker-first deployment with Cloudflare Tunnel recipe for instant public access
- ✓ Ready-to-import n8n workflow: `workflows/vapi-calendar-booking.json`
- ✓ Example system prompt with conversation guidelines and edge-case handling

---

## 🧪 Notes, Caveats & Next Steps

### Prerequisites

You must provision credentials separately (not included in this repo):
- **Vapi.ai API key** — [Sign up](https://vapi.ai)
- **Groq API key** — [Get LPU access](https://groq.com)
- **Google Calendar API credentials** — [Enable API](https://console.cloud.google.com/)

### Production Considerations

- Run n8n behind a process manager (systemd, supervisor) or orchestrator (Kubernetes) for resilience
- Secure webhooks with HMAC signatures and enforce HTTPS-only connections
- Consider rate limiting on the webhook endpoint to prevent abuse
- Log all booking events for compliance and audit trails

### Potential Enhancements

- [ ] Add integration tests for webhook handshakes and appointment conflicts
- [ ] Build a simple demo script to simulate calls for recruiter walkthroughs
- [ ] Multi-language support for greeting and confirmation messages
- [ ] Reschedule/cancel logic (not just creation)
- [ ] SMS reminders via Twilio

---

## 📜 License

MIT License — see [LICENSE](LICENSE) for details.
