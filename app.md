# 🧠 OpenCode Spec: Client-Only P2P Chat + Skill-Based Bot Engine (PWA)

---

# 🎯 Goal

Build a **production-grade, client-only PWA chat application** with:

* No backend / no central server
* Peer-to-peer communication (WebRTC)
* QR / address-based connection
* WhatsApp-like UI
* Local-first storage (IndexedDB)
* Skill-based bot engine (agents)
* Local LLM integration (optional)

---

# 🏗️ Architecture Overview

```text
PWA UI
 ↓
App Core
 ↓
IndexedDB (source of truth)
 ↓
Append-only message log
 ↓
Sync Engine
 ↓
WebRTC Transport (manual signaling)
 ↓
Peers
```

Parallel:

```text
Bot Engine
 ↓
Skill System
 ↓
LLM Runtime (optional)
```

---

# 📦 Project Structure

```bash
/app
  /core
    /protocol
    /sync
    /crypto
  /network
    /webrtc
    /discovery
  /storage
    indexeddb.js
  /bot
    runtime.js
    skills/
    context.js
  /ui
    components/
    screens/
  /pwa
    manifest.json
    service-worker.js
  main.js
  index.html
```

---

# 🔑 Core Requirements

## 1. Client-Only

* No backend APIs
* No cloud dependencies

## 2. Local-First

* All data stored in IndexedDB
* Network only syncs data

## 3. Deterministic Messaging

* Append-only log
* Hash-linked messages

---

# 🧠 Data Model

## Message

```json
{
  "id": "hash",
  "chat_id": "chat123",
  "from": "pubkey",
  "timestamp": 1710000000,
  "type": "text",
  "content": "Hello",
  "prev": "previous_hash",
  "signature": "signature"
}
```

---

## Chat

```json
{
  "id": "chat_id",
  "participants": ["pub1", "pub2"],
  "last_message": "hash"
}
```

---

## Bot

```json
{
  "id": "bot1",
  "skills": ["reply", "summarize", "calculator"],
  "memory": true,
  "policy": "auto"
}
```

---

# 🔗 Peer Connection

## Methods

### QR Code

Format:

```text
peer://<pubkey>?sdp=<encoded>
```

### Manual

* Paste peer address

---

## Flow

```text
Generate SDP
 ↓
Encode in QR
 ↓
Scan on peer
 ↓
Establish WebRTC
```

---

# 📡 WebRTC Layer

## Requirements

* Use DataChannels
* Reliable + ordered mode
* Manual signaling only (QR / copy-paste)

---

# 💾 Storage (IndexedDB)

## Tables

```text
messages
chats
bots
peers
memory
```

---

# 🔄 Sync Engine

## Responsibilities

* Exchange message logs
* Deduplicate by hash
* Maintain ordering via `prev`

---

## Protocol

```text
Peer A sends latest hash
Peer B sends missing messages
Merge into local DB
```

---

# 🤖 Bot Engine (Skill-Based)

## Architecture

```text
Message
 ↓
Intent Router
 ↓
Skill Selector
 ↓
Skill Execution
 ↓
Response
```

---

## Skill Interface

```js
skill = {
  name: "skill_name",
  run: async (input, context) => output
}
```

---

## Required Skills

* reply (LLM-based)
* summarize (LLM-based)
* calculator (deterministic)
* memory_read
* memory_write

---

## Bot Runtime

```js
runBot(bot, message, context):
  skill = routeIntent(message)
  result = executeSkill(skill, message, context)
  updateMemory()
  return result
```

---

## Intent Routing

Start simple:

```js
if (message.includes("calculate")) → calculator
if (message.includes("summarize")) → summarize
else → reply
```

---

# 🧠 LLM Integration

## Requirements

* Pluggable backend
* Prefer local WASM models

## Interface

```js
callLLM(messages) → response
```

---

# 💬 UI Requirements

## Screens

* Chat list
* Chat view
* Peer connect (QR scanner)
* Bot manager

## Features

* message bubbles
* timestamps
* delivery status (sent/delivered/seen)
* offline indicator

---

# ⚙️ PWA Requirements

## manifest.json

* installable app

## service-worker.js

* cache assets
* offline support

---

# 🔐 Security

* Ed25519 keypair per user
* Sign all messages
* Verify on receive

---

# ⚡ Performance

* paginate messages
* limit memory context
* lazy load chats

---

# 🚀 Development Phases

## Phase 1 (MVP)

* local chat UI
* IndexedDB storage
* bot runtime (basic)

## Phase 2

* WebRTC P2P (manual signaling)
* message sync

## Phase 3

* QR connect
* delivery ACK system

## Phase 4

* LLM integration
* advanced skills

---

# ⚠️ Constraints

* No guaranteed connectivity
* Manual peer bootstrap required
* LLM limited by device capability

---

# 💡 Final Definition

> A fully client-side, installable chat system where peers connect directly, data lives locally, and bots are skill-based agents executing on-device.

---

