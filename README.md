<div align="center">

# 🎯 Lead Capture Workflow

### *Intelligent form data capture, validation, and classification system*

[![Node.js](https://img.shields.io/badge/Node.js-≥18-43853D?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Express](https://img.shields.io/badge/Express-4.19-000000?style=for-the-badge&logo=express&logoColor=white)](https://expressjs.com)
[![Gemini AI](https://img.shields.io/badge/Gemini_AI-3.1_Flash-4285F4?style=for-the-badge&logo=google&logoColor=white)](https://aistudio.google.com)
[![Google Sheets](https://img.shields.io/badge/Google_Sheets-API-0F9D58?style=for-the-badge&logo=google-sheets&logoColor=white)](https://developers.google.com/sheets)
[![License](https://img.shields.io/badge/SE_445-HW3-FF6B6B?style=for-the-badge)]()

**Captures leads from a web form, validates them automatically, classifies them with AI, and persists everything to Google Sheets.**

[🚀 Quick Start](#-quick-start) •
[📋 Architecture](#-architecture) •
[⚙️ Setup](#-setup) •
[🧪 Testing](#-testing) •
[📊 Data Schema](#-data-schema)

---

</div>

## ✨ Key Features

<table>
<tr>
<td width="33%" align="center">

### 🛡️
**Smart Validation**

Catches missing fields and invalid emails — but **never deletes** them, just flags them.

</td>
<td width="33%" align="center">

### 🤖
**AI Classification**

Google Gemini auto-labels each lead by intent and urgency level.

</td>
<td width="33%" align="center">

### 📨
**Personalized Reply**

Generates a warm 2-3 sentence AI acknowledgment for every valid lead.

</td>
</tr>
</table>

---

## 📋 Architecture

```
                    ┌──────────────────────────┐
                    │   🌐 Web Form (Browser)   │
                    └────────────┬─────────────┘
                                 │
                          POST /webhook
                                 │
                                 ▼
                    ┌──────────────────────────┐
                    │   ⚡ Express Server        │
                    │      (server.js)          │
                    └────────────┬─────────────┘
                                 │
        ┌────────────────────────┼────────────────────────┐
        ▼                        ▼                        ▼
  ┌──────────┐           ┌──────────────┐         ┌──────────────┐
  │ ✓ Validate│           │ 🤖 Gemini AI  │         │ 📊 Sheets API │
  │          │           │              │         │              │
  │ • Name    │           │ • Intent      │         │ • 8 columns  │
  │ • Email   │           │ • Urgency     │         │ • All leads  │
  │ • Message │           │ • Reply       │         │ • Metadata   │
  └──────────┘           └──────────────┘         └──────────────┘
```

---

## 🚀 Quick Start

```bash
# 1. Install dependencies
npm install

# 2. Configure your .env file
cp .env.example .env
# (paste your credentials inside)

# 3. Start the server
npm start
```

> 🌟 Open **http://localhost:3000** in your browser to access the form.

---

## 🎯 AI Classification Matrix

<table>
<tr>
<th>Category</th>
<th>🟢 Low</th>
<th>🟡 Medium</th>
<th>🔴 High</th>
</tr>
<tr>
<td><b>📞 Support</b></td>
<td>General questions</td>
<td>Standard issues</td>
<td>Urgent / critical failures</td>
</tr>
<tr>
<td><b>💼 Sales</b></td>
<td>Info inquiries</td>
<td>Demo / pricing requests</td>
<td>Urgent / deadline-driven</td>
</tr>
<tr>
<td><b>🤝 Partnership</b></td>
<td>Initial outreach</td>
<td>Detailed discussions</td>
<td>Time-sensitive deals</td>
</tr>
</table>

---

## ⚙️ Setup

### 📦 Prerequisites

| Tool | Version | Link |
|------|---------|------|
| 🟢 Node.js | `≥ 18.0` | [nodejs.org](https://nodejs.org) |
| 📦 npm | bundled with Node | — |
| ☁️ Google Cloud | Account | [console.cloud.google.com](https://console.cloud.google.com) |
| 🧠 Google AI Studio | Account | [aistudio.google.com](https://aistudio.google.com) |

### 🔧 Google Sheets Configuration

<details>
<summary><b>📂 Step-by-step setup (click to expand)</b></summary>

#### 1️⃣ Create a Google Cloud Project
- Go to [Google Cloud Console](https://console.cloud.google.com)
- Click **"New Project"** → name it → **Create**

#### 2️⃣ Enable the Sheets API
- Left menu → **APIs & Services → Library**
- Search **"Google Sheets API"** → click **Enable**

#### 3️⃣ Create a Service Account
- **IAM & Admin → Service Accounts**
- **Create Service Account** → name it → **Done**

#### 4️⃣ Download the JSON Key
- Click your service account → **Keys** tab
- **Add Key → Create new key → JSON**

#### 5️⃣ Create and Share a Google Sheet
- Visit [sheets.google.com](https://sheets.google.com) → New spreadsheet
- Click **Share** → paste service account email → **Editor** → Send

</details>

### 🔑 .env File

```ini
# Google Sheets
GOOGLE_SERVICE_ACCOUNT_EMAIL=my-sa@my-project.iam.gserviceaccount.com
GOOGLE_PRIVATE_KEY="-----BEGIN PRIVATE KEY-----\n...\n-----END PRIVATE KEY-----\n"
GOOGLE_SPREADSHEET_ID=your_spreadsheet_id_here
GOOGLE_SHEET_NAME=Leads

# Gemini AI
GEMINI_API_KEY=AIza...

# Server
PORT=3000
```

> ⚠️ **Important:** Keep the `\n` characters in `GOOGLE_PRIVATE_KEY` as literal escape sequences — do not replace them with real line breaks.

---

## 🧪 Testing

### 🟢 Valid Lead

```bash
curl -X POST http://localhost:3000/webhook \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Jane Smith",
    "email": "jane@example.com",
    "message": "I urgently need pricing for 50 users, deadline this week"
  }'
```

**Response** (`200 OK`):

```json
{
  "success": true,
  "validationStatus": "Valid",
  "intent": "Sales",
  "urgency": "High",
  "aiResponse": "Hi Jane, thank you for reaching out! ...",
  "meta": { "sheetSaved": true, "processingMs": 834 }
}
```

### 🔴 Invalid Lead (still saved)

```bash
curl -X POST http://localhost:3000/webhook \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Test User",
    "email": "bademail",
    "message": "Hello"
  }'
```

**Response** (`422 Unprocessable Entity`):

```json
{
  "success": false,
  "validationStatus": "Invalid: invalid email format",
  "validationIssues": ["invalid email format"],
  "meta": { "sheetSaved": true }
}
```

---

## 📊 Data Schema

Every row in the Google Sheet contains these 8 columns:

| # | Column | Type | Description |
|---|--------|------|-------------|
| **A** | 🕐 Timestamp | ISO 8601 | When the request was received |
| **B** | 👤 Name | string | Lead's name |
| **C** | 📧 Email | string | Contact email address |
| **D** | 💬 Message | string | Lead's message |
| **E** | ✅ Validation Status | enum | `Valid` / `Invalid: <reason>` |
| **F** | 🎯 Intent | enum | `Support` / `Sales` / `Partnership` |
| **G** | ⚡ Urgency | enum | `High` / `Medium` / `Low` |
| **H** | 🤖 AI Response | string | Auto-generated reply from Gemini |

---

## 📁 Project Structure

```
SE445/
│
├── 🚪 server.js                 # Express server — webhook handler
├── 📦 package.json              # Dependencies and scripts
├── 🔒 .env                      # API credentials (never committed!)
│
├── 📂 services/
│   ├── 🤖 aiService.js          # Gemini — classification + reply generation
│   └── 📊 sheetsService.js      # Sheets — 8-column append
│
├── 📂 public/
│   └── 🌐 index.html            # Form UI
│
└── 📖 README.md                 # This file
```

---

## 🛣️ API Reference

### `POST /webhook`

Processes a form submission.

**Request Body:**
```json
{
  "name": "string (required)",
  "email": "string (required, valid format)",
  "message": "string (required)"
}
```

**Response Codes:**
- `200 OK` → Valid lead, classified and saved
- `422 Unprocessable Entity` → Invalid lead (still saved with flag)

### `GET /health`

Returns server status.

```json
{
  "status": "ok",
  "uptime": 3601,
  "timestamp": "2026-05-05T10:00:00.000Z"
}
```

---

## 🎓 Academic Information

> **Course:** SE 445 — Prompt Engineering
> **Assignment:** HW3 — Logic & Intelligent Processing
> **Topic:** Lead Capture Workflow
> **Goal:** Add validation and AI classification layers on top of HW2

### ✅ Requirements Met

- [x] **Validation:** Detects missing fields and invalid email formats
- [x] **Invalid leads preserved:** Stored with `Valid/Invalid` flag instead of being rejected
- [x] **AI Intent classification:** Support / Sales / Partnership
- [x] **AI Urgency classification:** High / Medium / Low
- [x] **Full metadata:** All AI outputs persisted to Sheets
- [x] **Testable:** Verified via curl and the web UI

---

<div align="center">

### 🚀 Stay tuned for the enhanced version in the Final Project

**Made with ⚡ by the SE 445 Group**

</div>
