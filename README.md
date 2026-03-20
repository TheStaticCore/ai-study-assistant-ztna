# 🤖 Zero Trust Secured Local AI Study Assistant

> A private, self-hosted AI study assistant secured with a Zero Trust access model — built on personal hardware for $0.

![Zero Trust](https://img.shields.io/badge/Architecture-Zero%20Trust-blue)
![ZTNA](https://img.shields.io/badge/Security-ZTNA-darkblue)
![Cloudflare](https://img.shields.io/badge/Cloudflare-Tunnel%20%2B%20Access-orange)
![Ollama](https://img.shields.io/badge/AI-Ollama%20%2B%20qwen3%3A14b-green)
![Platform](https://img.shields.io/badge/Platform-Windows-lightgrey)
![Cost](https://img.shields.io/badge/Cost-%240-brightgreen)

---

## 📌 Overview

I built my own AI study assistant and secured it with a Zero Trust access model. Accessible from any device, anywhere in the world — no open ports, no static IP exposed, no inbound firewall rules.

The same Zero Trust architecture enterprises pay six figures to implement, running on personal hardware for $0.

Turns out deploying it once taught me more than I expected.

---

## 🏗️ Architecture

![Architecture Diagram](diagram.png)

```
[User / PWA]
     │  HTTPS request via any device
     ▼
[Cloudflare Edge — Global Network]
     │  Bot Fight Mode    — blocks automated scanners
     │  Page Shield       — monitors client-side scripts
     │  Cloudflare Access — identity-based gate (email OTP)
     │                      unauthenticated traffic blocked here
     ▼
[Secure Transport]
     │  Cloudflare Tunnel — encrypted, outbound-only
     │  No open ports. No inbound rules. No static IP.
     ▼
[Local Hardware — Homelab]
     │
     ├── Open WebUI        (self-hosted chat interface, Docker)
     └── Ollama + qwen3:14b (local AI inference)
```

---

## 🛠️ Stack

| Component | Role |
|---|---|
| [Ollama](https://ollama.com) + qwen3:14b | Local AI inference on personal hardware |
| [Open WebUI](https://github.com/open-webui/open-webui) | Self-hosted chat interface (Docker container) |
| [Cloudflare Tunnel](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/) | Encrypted outbound-only connection, zero inbound exposure |
| [Cloudflare Access](https://developers.cloudflare.com/cloudflare-one/policies/access/) | Identity-based access control, email OTP verification |
| [Cloudflare Bot Fight Mode](https://developers.cloudflare.com/bots/get-started/free/) | Automated scanner and bot blocking |
| [Cloudflare Page Shield](https://developers.cloudflare.com/page-shield/) | Client-side script integrity monitoring |
| PWA (Progressive Web App) | Native-feeling mobile access, no App Store required |

---

## 🔐 Security Architecture

### Defense in depth

```
Internet traffic
      │
      ▼
 Bot Fight Mode        ← blocks automated scanners at the edge
      │
      ▼
 Page Shield           ← monitors client-side scripts
      │
      ▼
 Cloudflare Access     ← identity gate, email OTP required
      │                   unauthenticated requests die here
      ▼
 Cloudflare Tunnel     ← encrypted, outbound-only
      │                   no open ports, no inbound rules
      ▼
 Local machine         ← never directly exposed to the internet
```

### Security properties

| Property | Status |
|---|---|
| Attack surface at Layer 3 | Eliminated |
| Static IP exposed | None |
| Open inbound ports | None |
| Inbound firewall rules | Zero |
| Identity verification | Required (email OTP) |
| Unauthenticated access | Blocked at Cloudflare edge |

---

## 🚀 How to replicate this

### Prerequisites
- Windows PC (16GB+ RAM recommended for qwen3:14b)
- Docker Desktop for Windows
- A domain name (free with Cloudflare DNS)
- A free Cloudflare account

### 1. Install Ollama + pull model
```powershell
# Download installer from https://ollama.com
ollama pull qwen3:14b
ollama serve
```

### 2. Install Open WebUI via pip
```powershell
pip install open-webui
open-webui serve
```
Open WebUI will be available at `http://localhost:8080`

### 3. Set up Cloudflare Tunnel
```powershell
# Install cloudflared
winget install --id Cloudflare.cloudflared

# Authenticate and create tunnel
cloudflared tunnel login
cloudflared tunnel create ai-study-assistant

# Route to Open WebUI and run as Windows service
cloudflared tunnel route dns ai-study-assistant yourdomain.com
cloudflared service install
```

### 4. Configure Cloudflare Access
1. Cloudflare Zero Trust dashboard → Access → Applications → Add application
2. Set policy: allow only your email address
3. Enable one-time PIN (OTP) authentication

### 5. Enable Bot Fight Mode
1. Cloudflare Dashboard → Security → Bots
2. Toggle **Bot Fight Mode** on

### 6. Enable Page Shield
1. Cloudflare Dashboard → Security → Page Shield
2. Toggle on

### 7. Install as PWA on iPhone
1. Open your domain in Safari
2. Share → Add to Home Screen
3. Done — works like a native app, no App Store needed

---

## 💡 What I learned

Reading about Zero Trust is one thing. Actually implementing the identity layer, understanding why outbound-only tunnels eliminate inbound attack surface, and debugging authentication flows yourself — that's where the real understanding comes from.

Hands-on concepts covered:
- Zero Trust Network Access (ZTNA)
- Defense in depth
- Identity-based access control
- Encrypted tunneling
- Bot mitigation
- Client-side script monitoring
- Progressive Web Apps

---

## 📎 Resources

- [Cloudflare Zero Trust Docs](https://developers.cloudflare.com/cloudflare-one/)
- [Ollama Model Library](https://ollama.com/library)
- [Open WebUI Docs](https://docs.openwebui.com)

---

*Built on Windows. Secured with Cloudflare. Runs 24/7 for $0.*
