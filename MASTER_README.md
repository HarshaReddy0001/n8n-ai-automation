# 🤖 AI Automation Portfolio Projects

**AI Website Chatbot + Telegram Job Intelligence Agent**

This repository contains two production-ready AI automation systems
built using n8n, LLMs (Google Gemini), structured data sources, and live
deployment.

These projects demonstrate real-world AI integration --- full automation
pipelines, not just model usage.

------------------------------------------------------------------------

# 📌 Projects Included

## 1️⃣ AI Portfolio Website Chatbot --- "Nathan"

Live AI chatbot embedded in a portfolio website that answers recruiter
and technical questions dynamically.

### 🔗 Live Demo

https://harsha-porfolio.netlify.app

### 🧠 What It Does

-   Answers technical questions (Python, ML, NLP, Cloud, n8n)
-   Explains projects in detail
-   Summarizes work experience
-   Shares availability (C2C, W2, Full-time, Remote)
-   Provides LinkedIn and GitHub
-   Detects language automatically
-   Maintains short-term memory

------------------------------------------------------------------------

### 🏗 Architecture

Visitor (Website) │ ▼ n8n Chat Trigger (Webhook) │ ▼ AI Agent (Nathan) │
├── Google Gemini (LLM) ├── Simple Memory ├── Google Sheets (Structured
Q&A DB) └── Google Docs (Resume Source) │ ▼ Generated Response → Website

------------------------------------------------------------------------

### ⚙ Tech Stack

-   n8n (workflow automation)
-   Google Gemini (gemini-1.5-flash)
-   Google Sheets (Q&A database)
-   Google Docs (Resume integration)
-   Netlify (hosting)
-   HTML Chat Widget

------------------------------------------------------------------------

## 2️⃣ Telegram AI Job Intelligence Agent

An automated AI job assistant that monitors Telegram job groups, filters
relevant postings, analyzes job descriptions using LLMs, and sends
structured alerts.

### 🧠 What It Does

-   Monitors Telegram job groups
-   Extracts job descriptions automatically
-   Classifies roles using LLM
-   Matches required skills
-   Scores relevance
-   Filters spam
-   Sends structured alerts
-   Extendable to auto-apply

------------------------------------------------------------------------

### 🏗 Architecture

Telegram Trigger │ ▼ Message Parser │ ▼ AI Agent (Gemini) │ ├── Skill
Matching Logic ├── Job Classification ├── Relevance Scoring └──
Structured Output │ ▼ Filtered Job Alert

------------------------------------------------------------------------

# 📂 Repository Structure

AI-Automation-Portfolio/ │ ├── website-chatbot/ │ ├── workflow.json │
├── setup_guide.md │ ├── nathan_qa_database.xlsx │ └── README.md │ ├──
telegram-job-agent/ │ ├── workflow.json │ ├── setup_guide.md │ └──
README.md │ └── MASTER_README.md

------------------------------------------------------------------------

# 🚀 Why This Matters

-   Real automation systems
-   Live deployment
-   Multi-tool integration
-   LLM orchestration
-   Structured reasoning pipelines
-   Practical recruiter use cases

------------------------------------------------------------------------

# 🔮 Future Improvements

-   Vector database integration (Pinecone / Supabase)
-   Full RAG pipeline
-   Persistent memory (Redis)
-   Recruiter lead automation
-   Auto-application engine
-   Usage analytics dashboard

------------------------------------------------------------------------

# 👨‍💻 Author

Harshavardhan Reddy\
AI Automation Engineer \| Data Scientist

LinkedIn: https://www.linkedin.com/in/harshavardhan-reddy\
GitHub: https://github.com/harshavardhan
