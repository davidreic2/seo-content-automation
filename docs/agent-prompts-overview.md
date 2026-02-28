# AI Agent Prompts — Overview

This document provides a high-level summary of each AI agent's system prompt and role in the pipeline. Full prompts are embedded in the n8n workflow JSON files.

## Strategy Layer

### Content Strategist (GPT-4.1)
- **Input:** Live SERP data (DataForSEO), target keyword
- **System prompt focus:** 15+ years SEO expertise persona. Analyzes SERP to identify the winning content format (guide, list, comparison, analysis). Adapts outline structure to match what Google is rewarding for that specific query.
- **Key behaviors:** Identifies featured snippet type, maps People Also Ask questions, analyzes competitor content gaps, determines search intent (informational/transactional/commercial).

### Outline Generator (GPT-4.1)
- **Input:** Content strategist output + existing content inventory
- **System prompt focus:** Strict structural template — 3,500 words distributed across 6 H2 sections (450 words each) + intro (250) + FAQ (400) + conclusion (150). Cross-references existing content to avoid cannibalization and inserts internal links.
- **Key behaviors:** Enforces consistent article architecture while varying creative approach per keyword. Each H2 is typed (Featured Snippet target, Practical, Analysis, Comparison, Case Study, Problem-Solving).

## Writing Layer

### Writer 1 — Opening Specialist (GPT-4.1)
- **Sections:** Introduction (250 words) + H2#1 (450 words)
- **System prompt focus:** Hook creation (statistics, provocative questions, scenarios), Featured Snippet targeting (40-60 word direct answer), E-E-A-T credential establishment.
- **Constraint:** H2#1 must always be in question format matching a PAA query.

### Writer 2 — Content Developer A (GPT-4.1)
- **Sections:** H2#2 + H2#3 (900 words)
- **Input:** Outline + Writer 1's complete output
- **System prompt focus:** Core concept development, contextual depth, theory-to-practice balance. Never repeats Writer 1's information — always adds new value layers.

### Writer 3 — Content Developer B (GPT-4.1)
- **Sections:** H2#4 + H2#5 (900 words)
- **Input:** Outline + Writer 1 + Writer 2 outputs
- **System prompt focus:** Deep dive specialization, case studies with real metrics, thought leadership positioning. This is the "differentiator" — content that competitors don't have.

### Writer 4 — Closing Specialist (GPT-4.1)
- **Sections:** H2#6 + FAQ (8-12 questions) + Conclusion (1,000 words)
- **Input:** Outline + all 3 previous writers' outputs
- **System prompt focus:** Synthesis framework, snippet-optimized FAQ (30-50 word answers), consultative CTAs. FAQ questions target specific PAA queries and long-tail variations.

## Editing & Distribution Layer

### Brand Voice Editor (Gemini 2.5 Pro)
- **Input:** All 4 writers' combined output
- **System prompt focus:** Client-specific brand voice (in this case, an aviation training school). Inserts branded CTAs, distributes FAQ naturally throughout text (never as a separate section), enforces word count ceiling (1,200 words final), eliminates redundancies across writer sections.

### Design Coordinator (Claude 3.5 Sonnet)
- **Input:** Final article outline
- **System prompt focus:** Generates exactly 4 image prompts for DALL-E. Images must be hyper-realistic, positive/aspirational tone, people-inclusive when possible. Output is a strict JSON object with `imagem1` through `imagem4` keys.

### Social Media Writer (Claude 3.7 Sonnet)
- **Input:** Article outline
- **System prompt focus:** Creates a social media post that teases the article's key insight and drives click-through to the full blog post. Optimized for engagement over information delivery.
