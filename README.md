# voice-ai-receptionist-n8n
# 🎙️ Autonomous Voice AI Receptionist & Appointment Engine

[![Docker](https://img.shields.io/badge/Container-Docker-2496ED.svg)](https://www.docker.com/)
[![Workflow Engine](https://img.shields.io/badge/Workflow-n8n-FF6D5A.svg)](https://n8n.io/)
[![Voice Telephony](https://img.shields.io/badge/Voice-Vapi.ai-7B2CBF.svg)](https://vapi.ai/)
[![Inference](https://img.shields.io/badge/Inference-Groq_LPU-00B4D8.svg)](https://groq.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

A production-grade, sub-second latency conversational Voice AI receptionist built for healthcare and professional services (e.g., dental clinics, legal, real estate). Powered by **Vapi.ai**, containerized **n8n orchestration via Docker**, **Cloudflare Tunnel**, and **Google Workspace APIs**.

---

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