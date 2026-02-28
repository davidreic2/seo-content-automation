# 🚀 SEO Content Automation Pipeline

**From keyword research to published article — fully automated with AI agents and n8n.**

---

## What is this?

A system that takes a single keyword and automatically produces:

- ✅ A complete, SEO-optimized blog post (~3,500 words)
- ✅ 4 AI-generated images matched to the article content
- ✅ Ready-to-publish social media posts (integrated with Buffer)
- ✅ A delivery report with links to everything

**Zero manual work between stages.** The only human step is approving which keywords go into production — and even that is done directly in Google Sheets.

Built for a real client in the aviation training industry (Brazil). Reduced content production from **~8 hours to ~25 minutes per article**.

### 📊 Google Sheets as the control panel

The client never touches n8n, APIs, or any technical interface. **Google Sheets is the only interface they interact with.** Everything — from reviewing the content inventory, to approving keywords, to triggering production — happens through a single spreadsheet. They mark a column as "approved," and the pipeline does the rest. This was a deliberate design choice: the client's marketing team works in Sheets every day, so the automation meets them where they already are.

---

## How it works

The system is split into **4 automations** built in n8n:

### 🔍 Research Phase (3 automations)

| # | Automation | What it does |
|---|-----------|-------------|
| 1 | **Content Mapping** | Crawls the existing blog, extracts titles/topics/summaries, and builds a content inventory in Google Sheets. This prevents keyword cannibalization. |
| 2 | **Keyword Discovery** | An AI agent analyzes the content inventory, identifies gaps, and suggests 30 new keyword opportunities the blog should cover. |
| 3 | **Keyword Validation** | Pulls real search volume, competition index, and CPC data from the DataForSEO API for each keyword. Only data-backed keywords move forward. |

> After this phase, the team reviews the data and marks which keywords are approved for production.

### ✍️ Production Phase (1 automation, 7 AI agents inside)

The fourth automation is where the magic happens. For each approved keyword, it runs a **pipeline of 7 specialized AI agents**:

```
Keyword (approved)
    │
    ▼
┌─ STRATEGY ──────────────────────────────────────┐
│  1. Content Strategist    → Analyzes live Google │
│     (GPT-4.1)               results, People     │
│                              Also Ask, AI        │
│                              Overviews           │
│                                                  │
│  2. Outline Generator     → Creates a 6-section │
│     (GPT-4.1)               article structure   │
│                              with SEO targets    │
└──────────────────────────────────────────────────┘
    │
    ▼
┌─ WRITING (sequential chain) ────────────────────┐
│  3. Writer 1 (Opening)    → Intro + first       │
│  4. Writer 2 (Dev A)        section. Each writer │
│  5. Writer 3 (Dev B)        receives all         │
│  6. Writer 4 (Closing)      previous outputs     │
│     All GPT-4.1              for coherence       │
└──────────────────────────────────────────────────┘
    │
    ▼
┌─ EDITING ───────────────────────────────────────┐
│  Brand Voice Editor       → Consolidates all 4  │
│  (Gemini 2.5 Pro)           writers into one     │
│                              cohesive article    │
│                              with the client's   │
│                              brand voice + CTAs  │
└──────────────────────────────────────────────────┘
    │
    ├──▶ 📸 Design Coordinator (Claude 3.5 Sonnet)
    │       → Generates 4 image prompts from the article
    │       → DALL-E creates the images
    │       → Saved to Google Drive
    │
    ├──▶ 📱 Social Media Writer (Claude 3.7 Sonnet)
    │       → Creates ready-to-publish posts
    │       → Integrated with Buffer for scheduling
    │
    └──▶ 📋 Delivery Report (Google Docs)
            → Links to the article, images, and social posts
            → Summary of everything produced
```

---

## Why 4 writers instead of 1?

A single AI writing 3,500 words loses focus and coherence. By splitting the work into 4 sequential specialists:

- Each writer focuses on ~700-1,000 words of specific sections
- Writer 2 reads Writer 1's output. Writer 3 reads both. Writer 4 reads all three.
- This creates natural flow without repetition
- The Brand Voice Editor then consolidates everything into one unified piece

---

## Tech Stack

| Category | Tools |
|----------|-------|
| **Orchestration** | n8n (self-hosted) |
| **Writing & Strategy** | GPT-4.1, GPT-4.1-mini, GPT-4.1-nano |
| **Editing** | Gemini 2.5 Pro |
| **Creative** | Claude 3.5 Sonnet, Claude 3.7 Sonnet |
| **Images** | DALL-E (OpenAI) |
| **SEO Data** | DataForSEO (volume, competition, live SERP) |
| **Storage** | Google Sheets, Google Docs, Google Drive |
| **Distribution** | Buffer (social media scheduling) |

---

## SEO Methodology

This isn't just "AI writes a blog post." Each article is built on real search data:

- **Content gap analysis** — maps existing content before suggesting new topics
- **Live SERP analysis** — checks what's actually ranking on Google for each keyword
- **Featured Snippet targeting** — first section is structured to capture position zero
- **People Also Ask coverage** — FAQ section targets real questions users are searching
- **E-E-A-T signals** — each writer agent has specific instructions to demonstrate expertise and authority
- **Multi-channel distribution** — one keyword produces a blog post + images + social posts

---

## Results

| Metric | Before | After |
|--------|--------|-------|
| Time per article | ~8 hours | ~25 minutes |
| Monthly output | 4-6 articles | 20+ articles |
| Keyword research | 2-3 days manual | Automated + human approval |
| Content consistency | Variable | Consistent brand voice |
| Deliverables | Blog post only | Blog + images + social + report |

---

## Repository Structure

```
├── workflows/
│   ├── part-1-content-mapping.json      ← Crawls existing blog
│   ├── part-2-keyword-discovery.json    ← AI suggests keywords
│   ├── part-3-keyword-metrics.json      ← Pulls search volume data
│   └── part-4-content-production.json   ← 7-agent writing pipeline
├── docs/
│   ├── architecture-diagram.md          ← Full system architecture
│   └── agent-prompts-overview.md        ← Details on each AI agent
└── examples/
    └── sample-output-structure.md       ← Example of final output
```

---

## Setup

**Prerequisites:** n8n instance, OpenAI API key, Google Cloud OAuth2, DataForSEO API, Anthropic API key, Google AI API key (Gemini)

1. Import the 4 JSON workflows into n8n (Part 1 → Part 4)
2. Configure API credentials in each workflow
3. Set up your Google Sheets with tabs: `existing`, `suggested_keywords`, `production_queue`
4. Adjust DataForSEO location/language codes for your market

---

*Built by David as a production system for automated SEO content at scale. Demonstrates AI agent orchestration, SEO strategy, and workflow automation.*
