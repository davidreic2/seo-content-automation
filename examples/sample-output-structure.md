# Sample Output Structure

This document shows the deliverables produced by the pipeline for a single keyword run.

## Input

```
Keyword: "curso de comissário de bordo"
Status: publication_approved = "yes"
Search Volume: 12,100/mo
Competition: Medium
```

## Output Package

### 1. Google Docs — Blog Article (~1,200 words)

```
Title: [SEO-optimized H1 with target keyword]

├── Introduction (150 words)
│   ├── Hook (statistic or scenario)
│   ├── Problem identification
│   └── Solution + brand authority
│
├── H2 #1 — Featured Snippet Target (question format)
│   ├── Direct answer (40-60 words, snippet-optimized)
│   ├── H3: Expanded definition
│   ├── H3: Current context (2025)
│   └── H3: Practical implications
│
├── H2 #2 — Core Development
│   ├── H3: Subtopic A
│   ├── H3: Subtopic B
│   └── H3: Subtopic C
│
├── H2 #3 — Practical Application
│   ├── H3: Method/approach 1
│   ├── H3: Method/approach 2
│   └── H3: Advanced tips
│
├── H2 #4 — Deep Dive / Expert Analysis
│   ├── H3: Specialized insight 1
│   ├── H3: Specialized insight 2
│   └── H3: Advanced applications
│
├── H2 #5 — Case Studies / Real Experience
│   ├── H3: Case 1 with metrics
│   ├── H3: Case 2 with learnings
│   └── H3: Patterns & takeaways
│
├── H2 #6 — Synthesis & Implementation
│   ├── H3: Framework / checklist
│   ├── H3: Implementation steps
│   └── H3: Optimization tips
│
└── Conclusion (150 words)
    ├── Value recap
    ├── Next steps + CTA
    └── Brand connection

Note: FAQs are distributed naturally throughout
the text — not as a separate section.

CTAs placed at:
  - After ~300 words
  - Mid-article
  - Final conclusion
```

### 2. Google Drive Folder — 4 AI-Generated Images

```
📁 [Article Title]/
├── image-1.png  (matches Introduction / H2#1 context)
├── image-2.png  (matches H2#2-#3 context)
├── image-3.png  (matches H2#4-#5 context)
└── image-4.png  (matches H2#6 / conclusion context)

Style: Hyper-realistic, positive tone, people when relevant
Format: DALL-E generated, blog-ready dimensions
```

### 3. Social Media Post

```
Platform-ready post that:
- Teases a key insight from the article
- Creates curiosity to click through
- Includes relevant hashtags
- Optimized for engagement
```

### 4. Delivery Report (Google Docs)

```
Report: [Article Title]
─────────────────────────
Social Media Copy: [full text]

Blog Article Link: [Google Docs URL]

Images Folder: [Google Drive folder ID/URL]
```

## Google Sheets — Data Trail

After pipeline execution, the spreadsheet row is updated:

| Column | Value |
|--------|-------|
| keyword | curso de comissário de bordo |
| search_volume | 12,100 |
| competition_index | 0.45 |
| competition_level | Medium |
| avg_cpc | R$ 2.30 |
| approved | yes |
| publication_approved | yes |
| published | yes |
