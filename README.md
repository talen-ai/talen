# Talen — Lightweight Local Knowledge Base Management System

[![Version](https://img.shields.io/badge/version-2026.06-blue)]()
[![Platform](https://img.shields.io/badge/platform-Windows%20%7C%20macOS%20%7C%20Linux-lightgrey)]()
[![License](https://img.shields.io/badge/license-Proprietary-green)]()

> **Talen** is a lightweight, locally-deployed knowledge base management system. It runs as a desktop application with an embedded web server, offering document management, AI-powered Q&A, full-text search, and smart FAQ management — all with your data staying on your machine.

---

## Table of Contents

- [What is Talen](#what-is-talen)
- [Who It Is For](#who-it-is-for)
- [Features](#features)
- [Architecture Overview](#architecture-overview)
- [Technology Stack](#technology-stack)
- [Installation](#installation)
- [Editions](#editions)
- [Quick Start](#quick-start)
- [Official Website](#official-website)

---

## What is Talen

Talen is a **lightweight, local-first knowledge base management system** built with a desktop client + local web server architecture. Once installed, you access the full application through your browser while all data — documents, vector embeddings, chat history — is stored **locally on your machine** using SQLite.

Unlike cloud-based solutions such as Notion or Confluence, Talen is designed for **offline and local deployment**. Your data never leaves your computer unless you explicitly share it. Compared to platforms like Dify or FastGPT, Talen is significantly lighter-weight, easier to install, and ready to use within minutes.

At its core, Talen leverages **RAG (Retrieval-Augmented Generation)** technology: documents are parsed, chunked, and vectorized; when you ask a question, the system retrieves the most relevant document fragments and feeds them to an AI model to generate accurate, context-grounded answers. This approach eliminates AI "hallucination" by anchoring every answer in your actual documents.

---

## Who It Is For

| User Type | Typical Use Case |
|-----------|-----------------|
| **Individual Users** | Manage personal knowledge base, quickly retrieve and summarize documents |
| **Small Teams** | Collaborate on shared knowledge, ask AI questions across team documents |
| **Enterprises** | Turn internal policies, standards, and technical docs into a searchable Q&A system |
| **Developers** | Integrate knowledge base capabilities into third-party systems via REST API |

---

## Features

### Knowledge Base Management
- Create and manage multiple independent knowledge bases
- Each knowledge base has its own documents, chats, and permissions

### Multi-Format Document Import
- Supports **PDF**, **Word (.docx)**, **Markdown (.md)**, **Plain Text (.txt)**, **HTML**, and **Excel (.xlsx)**
- Import single files or entire folders in one go

### Automated Document Pipeline
- Documents are automatically parsed, intelligently chunked into semantically complete segments, and indexed for both keyword and vector search

### AI-Powered Q&A (RAG)
- Ask questions within a single knowledge base or across all knowledge bases (Global Smart Chat)
- Answers include **source citations** linking back to specific document paragraphs
- Streaming output for real-time response display

### Full-Text Search
- **Keyword search** with FTS5 engine — supports boolean operators (AND, OR, NOT)
- **Semantic search** via vector similarity
- **Cross-knowledge-base global search**

### FAQ Smart Library
- Manage high-frequency Q&A pairs with keyword matching and category filtering
- Batch import from Excel/CSV

### Multi-Model AI Support
- Integrates with **OpenAI, Baidu Wenxin, Alibaba Tongyi, DeepSeek, Google Gemini**, and more
- Configure API keys, base URLs, model names, and parameters through the UI

### Multi-User & Permissions (Professional & Enterprise)
- Role-based access control (admin / user)
- Per-knowledge-base read/write/delete permissions

### REST API (Enterprise)
- Full knowledge base capabilities exposed via HTTP API for third-party integration

### Privacy & Security
- All data stored locally — no upload to third-party clouds
- Privacy password protects chat history
- AES-256-GCM encrypted license files

### Backup & Restore
- Full data backup (database, vectors, config) packaged as a single ZIP file
- One-click restore from any backup

---

## Architecture Overview

```
┌─────────────────────────────────────────────────────┐
│                  Desktop Client (Tauri 2)            │
│   System Tray · Right-Click Menu · Auto-Start       │
│   Desktop Notifications · Auto-Updater              │
├─────────────────────────────────────────────────────┤
│              Local Web Server (Actix-web 4)          │
│   RESTful API · Document Pipeline · RAG Engine      │
│   FTS5 Search · Vector Store · Auth & License       │
├─────────────────────────────────────────────────────┤
│                   Browser (UI Layer)                 │
│   React 18 SPA · TailwindCSS · Responsive Layout    │
├─────────────────────────────────────────────────────┤
│                   Data Layer (SQLite)                │
│   kb.db: docs · chunks · vectors · chat · users     │
│   FTS5 Full-Text Index · BLOB Vector Storage        │
└─────────────────────────────────────────────────────┘
```

**Document Processing Pipeline:**

1. **Parser** — Extracts plain text using format-specific parsers (lopdf for PDF, docx-rs for Word, calamine for Excel, html2text for HTML)
2. **Chunker** — Splits text into semantically complete chunks (by paragraph, heading, or character count)
3. **Embedder** — Converts each chunk into a vector embedding via AI models
4. **Indexer** — Builds FTS5 full-text search index for fast keyword retrieval

**RAG Q&A Flow:**

```
User Question
    ↓
[Vectorize + Keyword Search]
    ↓
Retrieve Top-K relevant document chunks
    ↓
[Chunks + Question] → AI Model
    ↓
Answer with source citations
```

---

## Technology Stack

### Frontend

| Category       | Technology         | Version | Purpose              |
|----------------|--------------------|---------|----------------------|
| Framework      | React              | 18.3.1  | UI construction      |
| Build Tool     | Vite               | 5.4.2   | Dev & bundling       |
| Language       | TypeScript         | 5.5     | Type safety          |
| Routing        | react-router-dom   | 8.x     | Frontend routing     |
| Styling        | TailwindCSS        | 3.4     | CSS framework        |
| State Mgmt     | Zustand            | 4.5     | Global state         |
| Markdown       | react-markdown     | 9.0     | Markdown rendering   |
| Icons          | lucide-react       | 0.441   | Icon library         |

### Backend

| Category            | Technology       | Version | Purpose                    |
|---------------------|------------------|---------|----------------------------|
| Desktop Framework   | Tauri            | 2.x     | Desktop client shell       |
| Web Server          | actix-web        | 4       | HTTP REST API              |
| Database            | SQLite (rusqlite)| 0.31    | Persistent storage         |
| Full-Text Search    | FTS5             | Built-in| Full-text indexing         |
| HTTP Client         | reqwest          | 0.12    | AI API calls               |
| PDF Parser          | lopdf            | 0.32    | Document import            |
| Word Parser         | docx-rs          | 0.3     | Document import            |
| Excel Parser        | calamine         | 0.24    | Document import            |
| HTML Parser         | html2text        | 0.6     | Document import            |
| Encryption          | aes-gcm          | 0.10    | Data encryption            |
| Hashing             | sha2 / hmac / argon2 | —   | Signatures & passwords     |
| Serialization       | serde            | —       | Data serialization         |
| Async Runtime       | tokio            | 1.x     | Async support              |
| Vector Compute      | Custom (BLOB)    | —       | Vector storage & retrieval |

---

## Installation

Talen is distributed as a desktop application. Download the installer for your operating system from the official website.

### System Requirements

- **OS**: Windows 10+, macOS 12+, or Linux (x86_64)
- **RAM**: 4 GB minimum (8 GB recommended)
- **Disk**: 500 MB for application + storage for your documents
- **Browser**: Chrome 90+, Firefox 90+, Edge 90+, or Safari 15+

### Install Steps

1. Go to [https://talen.ink](https://talen.ink) and download the installer for your platform
2. Run the installer and follow the on-screen instructions
3. Launch Talen — the application will:
   - Initialize the SQLite database automatically
   - Prompt you to set up an admin account on first run
   - Start the local web service and open your default browser
4. Start creating knowledge bases and importing documents

---

## Editions

| Feature                          | Personal (Free) | Professional | Enterprise / Flagship |
|----------------------------------|:---------------:|:------------:|:---------------------:|
| Multi-knowledge-base management  | ✅              | ✅           | ✅                    |
| Document import & parsing        | ✅              | ✅           | ✅                    |
| AI-powered Q&A                   | ✅              | ✅           | ✅                    |
| Full-text search                 | ✅              | ✅           | ✅                    |
| FAQ smart library                | ✅              | ✅           | ✅                    |
| Multi-model AI support           | ✅              | ✅           | ✅                    |
| Lifetime upgrades                | ✅              | ✅           | ✅                    |
| Team collaboration (multi-user)  | —               | ✅           | ✅                    |
| Permissions management           | —               | ✅           | ✅                    |
| REST API                         | —               | —            | ✅                    |
| Dedicated technical support      | —               | ✅           | ✅                    |
| **Price**                        | **Free**        | **¥99/year** | **¥299/year**         |

- **Personal Edition**: Ideal for individual use — manage personal notes, documents, and get AI answers at no cost.
- **Professional Edition**: Designed for small teams that need multi-user collaboration, role-based access, and priority support.
- **Enterprise / Flagship Edition**: Full feature set including REST API integration for custom workflows and enterprise-grade support.

---

## Quick Start

1. **Install & launch** Talen (see [Installation](#installation))
2. **Create a knowledge base**: Click "+ Create Knowledge Base" in the sidebar
3. **Import documents**: Drag and drop or browse to select files (PDF, Word, Markdown, etc.)
4. **Ask questions**: Open the Smart Chat panel and type your question — AI will answer based on your documents
5. **Search**: Use the search bar to find specific content across all knowledge bases

---

## Official Website

| Resource      | URL                              |
|---------------|----------------------------------|
| Homepage      | https://talen.ink                |
| Purchase      | https://talen.ink/pricing        |
| License API   | https://talen.ink/license        |

---

## License

Talen is proprietary software. Personal Edition is free for personal use. Professional and Enterprise editions require a paid annual license. See the [Editions](#editions) section for feature comparison.

---

*© 2026 Talen Team. All rights reserved.*
