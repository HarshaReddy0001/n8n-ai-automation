# 🤖 Nathan — AI Portfolio Chatbot

An intelligent AI chatbot named **Nathan** embedded on a personal portfolio website, built using **n8n**, **Google Gemini**, **Google Sheets**, and **Google Docs**. Nathan answers visitor questions 24/7 — using a Q&A database from Google Sheets and a live resume from Google Docs to generate smart, accurate, personalized responses.

🌐 **Live Demo:** [harsha-porfolio.netlify.app](https://harsha-porfolio.netlify.app)

![Website Chatbot Screenshot](screenshot.png)

---

## 📌 Table of Contents

- [Project Overview](#project-overview)
- [Architecture](#architecture)
- [Tech Stack](#tech-stack)
- [How Nathan Works](#how-nathan-works)
- [Data Sources](#data-sources)
- [Google Sheets Structure](#google-sheets-structure)
- [Google Docs Resume Structure](#google-docs-resume-structure)
- [Agent Rules & Behavior](#agent-rules--behavior)
- [HTML Embed Code](#html-embed-code)
- [Folder Structure](#folder-structure)
- [How to Replicate This](#how-to-replicate-this)
- [Future Improvements](#future-improvements)

---

## Project Overview

Nathan is a conversational AI chatbot embedded directly into a personal portfolio website. When a visitor lands on the site, Nathan greets them and answers questions intelligently using two data sources:

- **Google Sheets** — a structured Q&A database with pre-defined questions and answers covering skills, projects, education, work preferences, LinkedIn, GitHub, and availability
- **Google Docs** — the actual resume, which Gemini reads to generate detailed, dynamic answers for complex questions

This means Nathan gives **short FAQ answers** for common questions and **detailed resume-based answers** for in-depth ones — all powered by Google Gemini.

---

## Architecture

```
Visitor (Browser)
      │
      ▼
┌──────────────────────────┐
│   Portfolio Website      │  ← Hosted on Netlify
│   n8n Chat Widget (CDN)  │  ← Injected via <script> tag
└────────────┬─────────────┘
             │ HTTP POST
             ▼
┌──────────────────────────┐
│        n8n Cloud         │
│                          │
│  ┌────────────────────┐  │
│  │   Chat Trigger     │  │  ← Receives visitor message
│  └─────────┬──────────┘  │
│            │              │
│  ┌─────────▼──────────┐  │
│  │     AI Agent       │  │  ← Nathan's brain
│  │   (93 Rules)       │  │
│  └──┬─────┬───────────┘  │
│     │     │              │
│  ┌──▼─┐ ┌─▼──────────┐  │
│  │LLM │ │   Tools    │  │
│  │    │ │            │  │
│  │Gem-│ │ ┌────────┐ │  │
│  │ini │ │ │Sheets  │ │  │  ← Q&A database (FAQ answers)
│  │    │ │ └────────┘ │  │
│  │    │ │ ┌────────┐ │  │
│  └────┘ │ │ Docs   │ │  │  ← Resume (detailed answers)
│         │ └────────┘ │  │
│         └────────────┘  │
└────────────┬─────────────┘
             │ AI Response
             ▼
┌──────────────────────────┐
│  Visitor sees Nathan's   │
│  reply on the website    │
└──────────────────────────┘
```

---

## Tech Stack

| Tool | Purpose |
|------|---------|
| **n8n** | Backend workflow automation & chat trigger |
| **Google Gemini** | LLM — generates smart responses from resume |
| **Google Sheets** | Q&A database — FAQ style questions & answers |
| **Google Docs** | Resume source — detailed professional information |
| **n8n Chat Widget** | Frontend chat UI via CDN |
| **Netlify** | Portfolio website hosting |
| **HTML / CSS / JS** | Portfolio website frontend |

---

## How Nathan Works

### Step 1 — Visitor Sends a Message
The visitor types a question on the portfolio website. The n8n chat widget sends it to the n8n webhook.

### Step 2 — AI Agent Receives the Message
Nathan's AI Agent node receives the question and decides which tool to use:
- **Simple question** (skills, availability, LinkedIn, GitHub) → reads from **Google Sheets**
- **Detailed question** (explain your experience, tell me about your projects) → reads from **Google Docs resume**

### Step 3 — Google Sheets Tool (FAQ Answers)
The agent reads the Q&A sheet, finds the best matching question, and returns the pre-written answer. Fast and accurate for common questions.

### Step 4 — Google Docs Tool (Resume-Based Answers)
For detailed questions, Gemini reads the actual resume document and **generates a natural, conversational answer** based on the resume content. This means even questions not in the Q&A sheet get answered intelligently.

### Step 5 — Gemini Generates the Final Response
Google Gemini takes the data from Sheets or Docs and formats it into a warm, professional, concise reply following Nathan's 93 rules.

### Step 6 — Nathan Replies to Visitor
The response is sent back to the chat widget and displayed instantly on the website.

---

## Data Sources

### 1. Google Sheets — Q&A Database
Used for: quick, pre-defined answers to common questions
Triggered when: visitor asks about skills, tools, work preference, availability, LinkedIn, GitHub

### 2. Google Docs — Resume
Used for: detailed, dynamic answers generated by Gemini
Triggered when: visitor asks complex or detailed questions about experience, projects, or background

---

## Google Sheets Structure

Create a Google Sheet with these exact column headers:

| category | question | answer |
|----------|---------|--------|
| skills | What programming languages do you know? | Python, JavaScript, SQL |
| skills | What automation tools do you use? | n8n, Make (Integromat) |
| skills | What AI tools have you worked with? | Google Gemini, LangChain, OpenAI |
| projects | What chatbots have you built? | WhatsApp bot, Telegram bot, Portfolio website chatbot using n8n |
| projects | What is your most impressive project? | AI-powered WhatsApp chatbot with inventory management and order taking |
| education | What is your educational background? | [Your degree and university] |
| education | What courses or certifications have you completed? | [Your certifications] |
| experience | How many years of experience do you have? | [Your experience] |
| experience | What kind of work have you done professionally? | [Your work history summary] |
| availability | Are you open to full-time roles? | Yes, I am actively looking for full-time opportunities |
| availability | Are you open to C2C or W2? | Open to both C2C and W2 arrangements |
| availability | Are you open to remote work? | Yes, fully remote preferred |
| availability | Are you open to relocation? | [Your preference] |
| availability | What is your work authorization status? | [Your status] |
| contact | Where can I find your LinkedIn? | linkedin.com/in/harshareddy0001 |
| contact | Where can I find your GitHub? | github.com/HarshaReddy0001 |
| contact | How can I contact you directly? | Reach out via LinkedIn for the fastest response |
| general | What type of work do you prefer? | AI automation, chatbot development, data analytics |
| general | What industries have you worked in? | [Your industries] |
| general | Are you available for freelance work? | Yes, open to freelance and contract projects |

> ✅ Add as many rows as you want — the more Q&A pairs you add, the smarter Nathan gets.

---

## Google Docs Resume Structure

Your resume in Google Docs should be clearly structured so Gemini can read and extract information easily. Use these sections:

```
HARSHAVARDHAN — Resume

SUMMARY
[2-3 sentence professional summary]

TECHNICAL SKILLS
- Programming: Python, JavaScript, SQL
- Automation: n8n, Make
- AI Tools: Google Gemini, LangChain
- Data: Pandas, Excel, Tableau
- Web: HTML, CSS, JavaScript

PROJECTS
1. WhatsApp AI Chatbot (n8n + Gemini + Google Sheets)
   - Built an intelligent WhatsApp bot that handles FAQs, inventory, and orders
   - Tech: n8n, WhatsApp Business API, Google Gemini, Google Sheets

2. Portfolio Website Chatbot — Nathan (n8n + Gemini)
   - Built an AI assistant embedded on personal portfolio
   - Tech: n8n, Google Gemini, Google Sheets, Google Docs, Netlify

3. Telegram AI Chatbot (n8n + Gemini)
   - [Description]

EDUCATION
[Your degree, university, graduation year]

WORK EXPERIENCE
[Your work history]

CERTIFICATIONS
[Your certifications]

WORK PREFERENCES
- Open to: Full-time, C2C, W2, Freelance
- Work type: Remote preferred
- Location: [Your location]
- Authorization: [Your work authorization]

CONTACT
- LinkedIn: linkedin.com/in/harshareddy0001
- GitHub: github.com/HarshaReddy0001
- Portfolio: harsha-porfolio.netlify.app
```

> ✅ The clearer your resume is structured, the better Gemini can generate answers from it.

---

## Agent Rules & Behavior

Nathan follows 93 detailed rules across 12 sections. See [`agent_rules.md`](agent_rules.md) for the complete system prompt to paste into n8n.

Key behaviors:
- Introduces himself as **Nathan**
- Uses **Google Sheets** for quick FAQ answers
- Uses **Google Docs resume** for detailed answers via Gemini
- Responds in the **visitor's language** automatically
- Politely declines off-topic questions
- Always suggests LinkedIn for direct contact

---

## HTML Embed Code

Add this at the bottom of your `index.html` just before `</body>`:

```html
<!-- Nathan Chatbot — Powered by n8n -->
<link href="https://cdn.jsdelivr.net/npm/@n8n/chat/dist/style.css" rel="stylesheet" />
<script type="module">
  import { createChat } from 'https://cdn.jsdelivr.net/npm/@n8n/chat/dist/chat.bundle.es.js';

  createChat({
    webhookUrl: 'YOUR_N8N_WEBHOOK_URL/chat'
  });
</script>
```

> ⚠️ Replace `YOUR_N8N_WEBHOOK_URL` with your actual n8n Chat Trigger webhook URL.

---

## Folder Structure

```
website-chatbot/
├── README.md             ← This file — project overview
├── setup_guide.md        ← Step-by-step setup instructions
├── workflow.json         ← n8n workflow (import this into n8n)
├── agent_rules.md        ← Nathan's 93 rules — paste into n8n AI Agent
└── screenshot.png        ← Website chatbot live screenshot
```

---

## How to Replicate This

1. Set up Google Sheets with the Q&A structure above
2. Copy your resume into Google Docs with the structure above
3. Import `workflow.json` into your n8n instance
4. Add credentials: Google Gemini API, Google Sheets OAuth, Google Docs OAuth
5. In the Google Sheets node — add your Sheet ID
6. In the Google Docs node — add your Doc ID
7. Copy the Chat Trigger webhook URL from n8n
8. Paste the HTML embed code into your `index.html`
9. Replace `YOUR_N8N_WEBHOOK_URL` with your actual webhook URL
10. Deploy to Netlify — Nathan is live!

For detailed steps, see [`setup_guide.md`](setup_guide.md)

---

## Future Improvements

- Add **appointment booking** tool so visitors can schedule a call with Harshavardhan
- Add **GitHub API** integration to show latest repos dynamically
- Add **LinkedIn API** to pull latest activity
- Log all visitor questions to Google Sheets for analytics
- Add **voice input** for accessibility
- Add **dark/light mode** toggle for the chat widget

---

## 👤 Author

**Harshavardhan**
- 🌐 Portfolio: [harsha-porfolio.netlify.app](https://harsha-porfolio.netlify.app)
- 💼 LinkedIn: [linkedin.com/in/harshareddy0001](https://linkedin.com/in/harshareddy0001)
- 🐙 GitHub: [github.com/HarshaReddy0001](https://github.com/HarshaReddy0001)

---

> Built with n8n + Google Gemini + Google Sheets + Google Docs. No custom backend required. Pure automation. ⚡
