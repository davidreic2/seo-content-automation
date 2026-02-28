# Architecture Diagram

## Full Pipeline — End to End

```
                              RESEARCH PHASE
                         (Parts 1, 2, 3 — async)
    ┌─────────────────────────────────────────────────────────┐
    │                                                         │
    │   [Blog URLs]                                           │
    │       │                                                 │
    │       ▼                                                 │
    │   ┌────────────────┐                                    │
    │   │  PART 1        │                                    │
    │   │  Content       │──▶ Google Sheets                   │
    │   │  Mapping       │    (content inventory)             │
    │   │  (GPT-4.1-mini)│                                    │
    │   └────────────────┘                                    │
    │           │                                             │
    │           ▼                                             │
    │   ┌────────────────┐                                    │
    │   │  PART 2        │                                    │
    │   │  Keyword       │──▶ Google Sheets                   │
    │   │  Discovery     │    (30 keyword suggestions)        │
    │   │  (GPT-4.1)     │                                    │
    │   └────────────────┘                                    │
    │           │                                             │
    │           ▼  (manual approval: team marks keywords)     │
    │   ┌────────────────┐                                    │
    │   │  PART 3        │                                    │
    │   │  Metrics &     │──▶ Google Sheets                   │
    │   │  Validation    │    (volume, CPC, competition)      │
    │   │  (DataForSEO)  │                                    │
    │   └────────────────┘                                    │
    │                                                         │
    └─────────────────────────────────────────────────────────┘
                              │
                    (manual approval: mark for publication)
                              │
                              ▼
                         PRODUCTION PHASE
                       (Part 4 — automated)
    ┌─────────────────────────────────────────────────────────┐
    │                                                         │
    │   For each approved keyword:                            │
    │                                                         │
    │   ┌─────────────────────────────────────────────┐       │
    │   │  SERP ANALYSIS                              │       │
    │   │  DataForSEO Live → Python processing        │       │
    │   │  → Content Strategist (GPT-4.1)             │       │
    │   │  → Outline Generator (GPT-4.1)              │       │
    │   └────────────────────┬────────────────────────┘       │
    │                        │                                │
    │          ┌─────────────┼─────────────┐                  │
    │          ▼             ▼             ▼                   │
    │   ┌──────────┐  ┌──────────┐  ┌──────────┐             │
    │   │ WRITING  │  │ VISUAL   │  │ SOCIAL   │             │
    │   │ TRACK    │  │ TRACK    │  │ TRACK    │             │
    │   │          │  │          │  │          │             │
    │   │ W1→W2→   │  │ Design   │  │ Social   │             │
    │   │ W3→W4→   │  │ Coord.   │  │ Media    │             │
    │   │ Editor   │  │ → 4×DALL │  │ Writer   │             │
    │   │          │  │ -E imgs  │  │          │             │
    │   └────┬─────┘  └────┬─────┘  └────┬─────┘             │
    │        │             │             │                    │
    │        ▼             ▼             ▼                    │
    │   Google Docs   Google Drive   Delivery Report          │
    │   (article)     (images)       (links to all assets)    │
    │                                                         │
    └─────────────────────────────────────────────────────────┘
```

## Part 4 — Writer Chain Detail

```
Outline Generator output
         │
         ▼
┌─────────────────┐
│  Writer 1       │  Introduction (250w) + H2#1 Featured Snippet (450w)
│  Opening        │  = 700 words
│  GPT-4.1        │
└────────┬────────┘
         │ passes full output ──▶
         ▼
┌─────────────────┐
│  Writer 2       │  H2#2 (450w) + H2#3 (450w)
│  Dev A          │  = 900 words
│  GPT-4.1        │  Context: outline + Writer 1
└────────┬────────┘
         │ passes full output ──▶
         ▼
┌─────────────────┐
│  Writer 3       │  H2#4 (450w) + H2#5 (450w)
│  Dev B          │  = 900 words
│  GPT-4.1        │  Context: outline + Writer 1 + Writer 2
└────────┬────────┘
         │ passes full output ──▶
         ▼
┌─────────────────┐
│  Writer 4       │  H2#6 (450w) + FAQ 8-12 questions (400w) + Conclusion (150w)
│  Closing        │  = 1,000 words
│  GPT-4.1        │  Context: outline + Writer 1 + Writer 2 + Writer 3
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Title Extract  │  GPT-4.1-nano (extracts H1 for Google Doc title)
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Brand Voice    │  Gemini 2.5 Pro
│  Editor         │  Consolidates all 4 blocks
│                 │  Applies client voice, CTAs, trims to 1,200w
└────────┬────────┘
         │
         ▼
    Google Docs (final article)
```

## Model Selection Rationale

```
Task Complexity vs. Cost Optimization

HIGH COMPLEXITY ──────────────────────────── LOW COMPLEXITY

  Gemini 2.5 Pro     GPT-4.1        GPT-4.1-mini    GPT-4.1-nano
  (editorial)        (strategy +     (extraction)     (title only)
                      writing)
  Claude 3.7         Claude 3.5
  (social copy)      (image prompts)

  ◄── Creative tasks     Structured tasks ──►
```
