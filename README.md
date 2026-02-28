# 🚀 SEO Content Automation Pipeline

**End-to-end AI-powered SEO content production system built with n8n**

An automated pipeline that takes a single approved keyword and produces a fully optimized blog post, AI-generated images, social media copy, and a delivery report — with zero manual intervention between stages.

Built for a real client in the **aviation training industry** (Brazil), this system reduced content production time from ~8 hours to ~25 minutes per article while maintaining editorial quality standards.

---

## 📋 Table of Contents

- [System Overview](#system-overview)
- [Architecture](#architecture)
- [Workflow Breakdown](#workflow-breakdown)
  - [Part 1 — Content Mapping Engine](#part-1--content-mapping-engine)
  - [Part 2 — Keyword Discovery Agent](#part-2--keyword-discovery-agent)
  - [Part 3 — Keyword Metrics & Validation](#part-3--keyword-metrics--validation)
  - [Part 4 — Content Production Pipeline](#part-4--content-production-pipeline)
- [AI Agent Architecture (Part 4 Deep Dive)](#ai-agent-architecture-part-4-deep-dive)
- [Tech Stack & Integrations](#tech-stack--integrations)
- [SEO Strategy & Methodology](#seo-strategy--methodology)
- [Results](#results)
- [Setup & Configuration](#setup--configuration)

---

## System Overview

```
┌─────────────────────────────────────────────────────────────────────┐
│                    SEO CONTENT AUTOMATION PIPELINE                   │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  PART 1             PART 2              PART 3           PART 4     │
│  Content Mapping    Keyword Discovery   Volume Lookup    Production  │
│                                                                     │
│  ┌──────────┐      ┌──────────┐       ┌──────────┐    ┌──────────┐ │
│  │ Crawl    │      │ AI Agent │       │ DataFor  │    │ 7 AI     │ │
│  │ existing │ ───▶ │ suggests │ ───▶  │ SEO API  │───▶│ Agents   │ │
│  │ content  │      │ 30 KWs   │       │ metrics  │    │ pipeline │ │
│  └──────────┘      └──────────┘       └──────────┘    └──────────┘ │
│       │                 │                  │               │        │
│       ▼                 ▼                  ▼               ▼        │
│  Google Sheets     Google Sheets      Google Sheets   Google Docs   │
│  (inventory)       (suggestions)      (validated)    + Drive + Imgs │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Architecture

### Data Flow

```
Existing Blog URLs
        │
        ▼
[Part 1] AI extracts: title, summary, topics ──▶ Google Sheets (inventory)
        │
        ▼
[Part 2] AI analyzes content gaps ──▶ 30 keyword suggestions ──▶ Sheets
        │
        ▼
[Part 3] DataForSEO API: volume, competition, CPC ──▶ Sheets (enriched)
        │
        ▼
        │  (Manual approval gate: team marks "approved" keywords)
        │
        ▼
[Part 4] For each approved keyword:
        ├──▶ SERP Analysis (DataForSEO live SERP)
        ├──▶ Content Strategy (AI Outline Generator)
        ├──▶ 4 Sequential Writers (Opening → Dev A → Dev B → Closing)
        ├──▶ Brand Voice Editor (final consolidation)
        ├──▶ Image Generation (4 AI images per article)
        ├──▶ Social Media Copy
        └──▶ Delivery Report (Google Docs + Drive folder)
```

### Design Principles

| Principle | Implementation |
|-----------|---------------|
| **Content-gap-first** | Part 1 maps what exists before Part 2 suggests what's missing |
| **Data-driven decisions** | Keywords are validated with real volume/competition data before production |
| **Human-in-the-loop** | Manual approval gate between research (Parts 1-3) and production (Part 4) |
| **Specialist agents** | Each AI agent has a single focused role with domain-specific prompts |
| **Sequential context** | Each writer receives the full output of all previous writers for coherence |
| **Multi-channel output** | One keyword → blog post + images + social copy + delivery report |

---

## Workflow Breakdown

### Part 1 — Content Mapping Engine

> **Goal:** Build a structured inventory of all existing blog content to prevent cannibalization and identify opportunities.

```
Manual Trigger
    │
    ▼
Loop Over Items (batch processing)
    │
    ▼
AI Agent (GPT-4.1-mini)
    │  Reads each URL
    │  Extracts: link, title, summary, main topics
    │
    ▼
Structured Output Parser (JSON schema enforcement)
    │
    ▼
Google Sheets ("existing content" tab)
```

**Key Details:**
- Processes blog URLs in batches to respect rate limits
- AI agent reads the live page content and extracts structured metadata
- Output schema is enforced via n8n's Structured Output Parser — no malformed data reaches the sheet
- Creates a canonical inventory that all downstream workflows reference

---

### Part 2 — Keyword Discovery Agent

> **Goal:** Use AI to analyze the existing content inventory and generate 30 high-potential keyword opportunities.

```
Google Sheets (existing content)
    │
    ▼
Aggregate (combine all content into single context)
    │
    ▼
AI Agent (GPT-4.1) — SEO Specialist persona
    │  System prompt: "Think in terms potential students
    │  and aviation professionals would search"
    │
    ▼
Structured Output Parser
    │  Enforces: array of { "keyword": string }
    │
    ▼
Split Out (one item per keyword)
    │
    ▼
Google Sheets ("suggested keywords" tab)
```

**Key Details:**
- The agent receives the full content inventory as context, ensuring suggestions don't overlap with existing articles
- Domain-specific prompting guides keyword discovery toward the client's target audience
- Each keyword is written as a separate row for downstream processing
- Generates 30 adjacent keyword opportunities per run

---

### Part 3 — Keyword Metrics & Validation

> **Goal:** Enrich approved keywords with search volume, competition index, and CPC data for prioritization.

```
Google Sheets (filter: approved = "yes")
    │
    ▼
HTTP Request → DataForSEO API
    │  Endpoint: Google Ads keyword data
    │  Location: Brazil (2076)
    │  Language: pt-BR
    │
    ▼
Extract: search_volume, competition_index,
         competition_level, avg_cpc
    │
    ▼
Google Sheets (update same row with metrics)
```

**Key Details:**
- Only processes keywords manually marked as approved — human quality gate
- Uses DataForSEO's Google Ads endpoint for reliable volume data
- Geo-targeted to Brazil (location code 2076) with Portuguese language setting
- Results update the same spreadsheet row, creating a single source of truth

---

### Part 4 — Content Production Pipeline

> **Goal:** Transform each validated keyword into a complete, publish-ready content package.

This is the most complex workflow — a **multi-agent AI pipeline** with 3 parallel tracks running simultaneously.

```
Google Sheets (filter: publication_approved = "yes" AND published = "no")
    │
    ▼
Loop Over Items (one keyword at a time)
    │
    ├──────────────────────────────────────────────────┐
    │           TRACK 1: SEO STRATEGY                  │
    │                                                  │
    │  DataForSEO Live SERP ──▶ Code Node (Python)     │
    │  (organic results,        (processes SERP into   │
    │   People Also Ask,         structured briefing)  │
    │   AI Overviews,                   │              │
    │   related searches,               ▼              │
    │   videos, local pack)    AI Content Strategist   │
    │                          (GPT-4.1)               │
    │                                   │              │
    │                                   ▼              │
    │                     Existing Content (Sheets)     │
    │                          + Aggregate             │
    │                                   │              │
    │                                   ▼              │
    │                        Outline Generator          │
    │                          (GPT-4.1)               │
    │                                                  │
    ├──────────────────────────────────────────────────┤
    │           TRACK 2: CONTENT WRITING               │
    │                                                  │
    │  Writer 1 ──▶ Writer 2 ──▶ Writer 3 ──▶ Writer 4│
    │  (Opening)   (Dev A)      (Dev B)     (Closing)  │
    │  700 words   900 words    900 words   1000 words │
    │                                                  │
    │                     │                            │
    │                     ▼                            │
    │              Title Extractor (GPT-4.1-nano)      │
    │                     │                            │
    │                     ▼                            │
    │              Brand Voice Editor (Gemini 2.5 Pro) │
    │              (consolidation + CTA insertion)     │
    │                     │                            │
    │                     ▼                            │
    │              Google Docs (final article)         │
    │                                                  │
    ├──────────────────────────────────────────────────┤
    │           TRACK 3: VISUAL + DISTRIBUTION         │
    │                                                  │
    │  Design Coordinator (Claude 3.5 Sonnet)          │
    │  ── generates 4 image prompts from outline       │
    │         │                                        │
    │         ▼                                        │
    │  ┌─────┬─────┬─────┬─────┐                      │
    │  │Img 1│Img 2│Img 3│Img 4│  (DALL-E, parallel)  │
    │  └──┬──┴──┬──┴──┬──┴──┬──┘                      │
    │     └─────┴─────┴─────┘                          │
    │              │                                   │
    │              ▼                                   │
    │     Google Drive (dedicated folder per article)  │
    │              │                                   │
    │              ▼                                   │
    │     Social Media Agent (Claude 3.7 Sonnet)       │
    │              │                                   │
    │              ▼                                   │
    │     Delivery Report (Google Docs)                │
    │     ── blog text link                            │
    │     ── social media copy                         │
    │     ── image folder link                         │
    │                                                  │
    └──────────────────────────────────────────────────┘
```

---

## AI Agent Architecture (Part 4 Deep Dive)

The content production pipeline uses **7 specialized AI agents** in a carefully designed sequence. Each agent has a distinct role, receives only the context it needs, and passes its output forward.

### Strategy Layer

| Agent | Model | Role | Output |
|-------|-------|------|--------|
| **Content Strategist** | GPT-4.1 | Analyzes live SERP data (top 10 results, PAA, AI Overviews) and builds a content brief | Structured briefing with keyword gaps, search intent, competitor analysis |
| **Outline Generator** | GPT-4.1 | Creates a 6-section article structure optimized for the identified content format | Full outline: H1, 6×H2 with 3×H3 each, FAQ targets, meta description |

### Writing Layer (Sequential Chain)

| Agent | Model | Sections | Words | Focus |
|-------|-------|----------|-------|-------|
| **Writer 1 — Opening** | GPT-4.1 | Intro + H2#1 | 700 | Hook, Featured Snippet targeting, E-E-A-T establishment |
| **Writer 2 — Dev A** | GPT-4.1 | H2#2 + H2#3 | 900 | Core concepts, contextual depth, LSI keyword distribution |
| **Writer 3 — Dev B** | GPT-4.1 | H2#4 + H2#5 | 900 | Deep dive expertise, case studies, authority building |
| **Writer 4 — Closing** | GPT-4.1 | H2#6 + FAQ + Conclusion | 1,000 | Synthesis, 8-12 FAQ (snippet-optimized), CTAs |

### Editing & Distribution Layer

| Agent | Model | Role |
|-------|-------|------|
| **Brand Voice Editor** | Gemini 2.5 Pro | Consolidates 4 writer outputs, applies client brand voice, inserts CTAs, trims to target length |
| **Design Coordinator** | Claude 3.5 Sonnet | Reads final article, generates 4 contextual image prompts (hyper-realistic, positive tone) |
| **Social Media Writer** | Claude 3.7 Sonnet | Creates social media post from the outline to drive blog traffic |

### Why This Architecture Works

**Specialist over generalist:** Each writer handles ~900 words of a specific article section. A single agent writing 3,500 words would lose focus and coherence. By splitting into 4 sequential specialists, each agent can deeply optimize its section while maintaining awareness of what came before.

**Sequential context passing:** Writer 2 receives Writer 1's output. Writer 3 receives both. Writer 4 receives all three. This creates natural continuity without redundancy.

**Cross-model strategy:** Different models for different strengths:
- GPT-4.1 for structured SEO writing (strong instruction following)
- Gemini 2.5 Pro for final editing (excellent at synthesis and revision)
- Claude 3.5/3.7 Sonnet for creative tasks (image prompts, social copy)

---

## Tech Stack & Integrations

### Automation Platform
- **n8n** (self-hosted) — workflow orchestration, batch processing, error handling

### AI Models
| Model | Usage | Why |
|-------|-------|-----|
| GPT-4.1 | Strategy, outlines, writing (6 agents) | Best-in-class instruction following for structured SEO content |
| GPT-4.1-mini | Content mapping (Part 1) | Cost-efficient for extraction tasks |
| GPT-4.1-nano | Title extraction | Minimal task, minimal cost |
| Gemini 2.5 Pro | Final brand voice editing | Strong at synthesis and editorial revision |
| Claude 3.5 Sonnet | Design coordination | Creative prompt generation |
| Claude 3.7 Sonnet | Social media copy | Natural, engaging tone for social content |
| DALL-E (OpenAI) | Image generation (4 per article) | Hyper-realistic style matching client brand |

### APIs & Services
| Service | Usage |
|---------|-------|
| **DataForSEO** | Keyword volume/competition data + Live SERP analysis (organic, PAA, AI Overviews) |
| **Google Sheets** (OAuth2) | Central data store — content inventory, keyword pipeline, approval gates |
| **Google Docs** (OAuth2) | Article delivery + production reports |
| **Google Drive** (OAuth2) | Image storage (auto-created folders per article) |

### Data Processing
- **Python** (n8n Code node) — SERP data processing, competitor analysis, content gap identification, semantic keyword extraction
- **JavaScript** (n8n Code node) — Data transformation, JSON normalization

---

## SEO Strategy & Methodology

This pipeline implements several modern SEO principles that go beyond traditional keyword stuffing:

### Content Gap Analysis
Part 1 maps the entire existing content library before any new content is planned. This prevents keyword cannibalization and ensures every new article adds unique value.

### SERP-Driven Content Architecture
Part 4's strategy layer doesn't just target a keyword — it analyzes the **live SERP** to understand:
- What content format is winning (guide vs. list vs. comparison)
- What questions users are asking (People Also Ask)
- Whether AI Overviews are present (signals Google's content expectations)
- What competitors are covering (and what they're missing)

### Featured Snippet Optimization
Writer 1 is specifically designed to capture Featured Snippets. The H2#1 always:
- Uses a question format matching PAA queries
- Opens with a 40-60 word direct answer (snippet-friendly length)
- Expands with structured H3 sub-sections

### E-E-A-T Signals
Each writer agent includes E-E-A-T (Experience, Expertise, Authoritativeness, Trustworthiness) requirements in their system prompts:
- **Writer 1:** Establishes credentials in the introduction
- **Writer 2-3:** Demonstrates expertise through depth and cases
- **Writer 4:** Consolidates authority with FAQ coverage and practical synthesis

### Multi-Format Output
A single keyword produces assets for multiple channels:
- Long-form blog post (SEO-optimized)
- 4 contextual images (blog + social media use)
- Social media post (drives traffic to the article)
- Delivery report (team coordination)

---

## Results

Deployed in production for an aviation training school blog in Brazil:

| Metric | Before | After |
|--------|--------|-------|
| Content production time | ~8 hours/article | ~25 minutes/article |
| Monthly article output | 4-6 articles | 20+ articles |
| Keyword research cycle | 2-3 days manual | Automated + approval gate |
| Content consistency | Variable (multiple freelancers) | Consistent brand voice |
| Multi-channel assets | Blog only | Blog + images + social + report |

---

## Setup & Configuration

### Prerequisites
- n8n instance (self-hosted or cloud)
- OpenAI API key (GPT-4.1 access)
- Google Cloud OAuth2 credentials (Sheets, Docs, Drive)
- DataForSEO API credentials
- Anthropic API key (Claude models)
- Google AI API key (Gemini)

### Google Sheets Structure

The system uses a single Google Sheets workbook with multiple tabs:

| Tab | Purpose | Key Columns |
|-----|---------|-------------|
| `existing` | Content inventory (Part 1 output) | url, title, summary, topics |
| `suggested_keywords` | AI-generated keywords (Part 2 output) | keyword, approved (yes/no), search_volume, competition |
| `production_queue` | Approved for production (Part 4 input) | keyword, publication_approved, published |

### Workflow Import

1. Import each JSON file into n8n in order (Part 1 → Part 4)
2. Configure credentials for each integration node
3. Update the Google Sheets document ID in all workflow nodes
4. Adjust the DataForSEO location/language codes for your target market

---

## Repository Structure

```
├── README.md
├── workflows/
│   ├── part-1-content-mapping.json
│   ├── part-2-keyword-discovery.json
│   ├── part-3-keyword-metrics.json
│   └── part-4-content-production.json
├── docs/
│   ├── architecture-diagram.md
│   └── agent-prompts-overview.md
└── examples/
    └── sample-output-structure.md
```

---

## About

Built by **David** as a production system for SEO content automation. This pipeline demonstrates the intersection of **technical SEO strategy**, **AI agent orchestration**, and **workflow automation** — turning keyword research into publish-ready, multi-channel content at scale.

### Key Skills Demonstrated
- n8n workflow design (complex multi-agent pipelines)
- AI prompt engineering (specialized agents with role-specific system prompts)
- SEO technical strategy (SERP analysis, content gap methodology, Featured Snippet targeting)
- API integration (DataForSEO, OpenAI, Anthropic, Google Workspace)
- Data pipeline architecture (structured data flow with human approval gates)
- Multi-model AI orchestration (GPT-4.1 + Gemini + Claude in a single pipeline)

---

*Client name redacted. Industry context: aviation training school in Brazil (ANAC-certified flight attendant and airport agent courses).*
