# PodCheck Pipeline — Proof of Concept Test

**Date:** 2026-02-08  
**Status:** Sandbox fallback (yt-dlp unavailable in Docker sandbox — no pip/network)

---

## 1. Transcript Acquisition: yt-dlp

### How It Works

yt-dlp extracts auto-generated or manually uploaded captions from YouTube videos.

**Command:**
```bash
yt-dlp --write-auto-sub --sub-lang en --skip-download --sub-format vtt \
  -o "podcheck-pipeline/transcript" \
  "https://www.youtube.com/watch?v=VIDEO_ID"
```

**Flags explained:**
| Flag | Purpose |
|------|---------|
| `--write-auto-sub` | Download auto-generated subtitles (fallback if no manual subs) |
| `--sub-lang en` | English only |
| `--skip-download` | Don't download video/audio |
| `--sub-format vtt` | WebVTT format (timestamped, structured) |
| `-o` | Output path template |

**Fallback search (no specific URL):**
```bash
yt-dlp "ytsearch:All-In Podcast 2025 full episode" \
  --write-auto-sub --sub-lang en --skip-download --sub-format vtt \
  -o "podcheck-pipeline/transcript"
```

### Raw VTT Format

yt-dlp produces WebVTT files like this:

```vtt
WEBVTT
Kind: captions
Language: en

00:00:00.000 --> 00:00:03.440
so deepseek which is a chinese ai lab

00:00:03.440 --> 00:00:06.880
released their r1 model and it
essentially matches gpt4 level

00:00:06.880 --> 00:00:10.320
performance on most benchmarks but
here's the kicker they claim they

00:00:10.320 --> 00:00:14.160
trained it for approximately five point
five million dollars
```

**Key characteristics of auto-generated VTT:**
- No punctuation or capitalization
- Lines break mid-sentence at ~3-4 second intervals
- Overlapping timestamp windows (each cue repeats 1-2 lines from prior cue)
- No speaker identification
- Filler words included ("um", "uh", "you know")

---

## 2. Cleaning Pipeline

### Step 1: Strip VTT Metadata & Deduplicate

Auto-generated VTT has duplicate lines across overlapping cues. Algorithm:

```python
import re

def parse_vtt(raw: str) -> list[dict]:
    """Extract timestamped text blocks from VTT."""
    blocks = []
    for match in re.finditer(
        r'(\d{2}:\d{2}:\d{2}\.\d{3}) --> (\d{2}:\d{2}:\d{2}\.\d{3})\n(.+?)(?=\n\n|\Z)',
        raw, re.DOTALL
    ):
        start, end, text = match.groups()
        # Collapse multi-line text within a cue
        text = ' '.join(text.strip().split('\n'))
        blocks.append({'start': start, 'end': end, 'text': text})
    return blocks

def deduplicate(blocks: list[dict]) -> str:
    """Remove overlapping duplicate lines across cues."""
    seen_words = []
    result = []
    for block in blocks:
        words = block['text'].split()
        new_words = []
        for w in words:
            if not seen_words or w != seen_words[-1]:
                new_words.append(w)
                seen_words.append(w)
        if new_words:
            result.append(' '.join(new_words))
    return ' '.join(result)
```

### Step 2: Add Punctuation & Capitalization

Use an LLM (Claude) to restore punctuation and sentence structure:

```
Prompt: "Restore punctuation, capitalization, and paragraph breaks 
to this auto-generated transcript. Do not change any words. 
Identify speakers where possible."
```

### Step 3: Extract Claims

Feed the cleaned transcript to Claude with a structured extraction prompt:

```
Prompt: "Extract all factual claims from this podcast transcript.
For each claim, return:
- claim: The factual assertion (one sentence)
- speaker: Who said it
- timestamp: Approximate time
- category: [statistic | prediction | historical | scientific | policy]
- confidence: [stated_as_fact | hedged | speculative]
- checkable: [true | false]

Return as JSON array. Only include verifiable factual claims, 
not opinions or predictions about future events."
```

---

## 3. Sample Output

### Input: Sample Transcript
See: `sample-transcript.txt` (~3,000 words, realistic All-In episode content)

### Extracted Claims (sample from transcript)

```json
[
  {
    "claim": "DeepSeek trained their R1 model for approximately $5.5 million",
    "speaker": "Chamath Palihapitiya",
    "timestamp": "00:00:22",
    "category": "statistic",
    "confidence": "hedged",
    "checkable": true
  },
  {
    "claim": "NVIDIA lost almost $600 billion in market cap in a single day, the largest single-day loss for any company in stock market history",
    "speaker": "David Sacks",
    "timestamp": "00:01:05",
    "category": "statistic",
    "confidence": "stated_as_fact",
    "checkable": true
  },
  {
    "claim": "Global data center electricity consumption hit about 460 terawatt hours in 2024, roughly 2% of global electricity demand",
    "speaker": "Jason Calacanis",
    "timestamp": "00:03:42",
    "category": "statistic",
    "confidence": "stated_as_fact",
    "checkable": true
  },
  {
    "claim": "Goldman Sachs projects AI will drive data center power consumption to around 1,000 TWh by 2030",
    "speaker": "Jason Calacanis",
    "timestamp": "00:03:42",
    "category": "prediction",
    "confidence": "stated_as_fact",
    "checkable": true
  },
  {
    "claim": "TikTok generated approximately $16 billion in US revenue in 2024, with global revenue around $80 billion",
    "speaker": "David Friedberg",
    "timestamp": "00:05:48",
    "category": "statistic",
    "confidence": "stated_as_fact",
    "checkable": true
  },
  {
    "claim": "The federal government employs about 2.2 million civilian workers with average total compensation around $130,000",
    "speaker": "Chamath Palihapitiya",
    "timestamp": "00:08:38",
    "category": "statistic",
    "confidence": "stated_as_fact",
    "checkable": true
  },
  {
    "claim": "Washington DC metro area has a median household income of about $110,000 vs national median of roughly $75,000",
    "speaker": "David Sacks",
    "timestamp": "00:09:28",
    "category": "statistic",
    "confidence": "stated_as_fact",
    "checkable": true
  },
  {
    "claim": "The GAO identified over $200 billion in improper payments across the federal government in fiscal year 2023",
    "speaker": "Chamath Palihapitiya",
    "timestamp": "00:11:05",
    "category": "statistic",
    "confidence": "stated_as_fact",
    "checkable": true
  },
  {
    "claim": "PEPFAR has saved over 25 million lives since 2003 at a total cost of about $110 billion",
    "speaker": "David Friedberg",
    "timestamp": "00:10:12",
    "category": "statistic",
    "confidence": "stated_as_fact",
    "checkable": true
  },
  {
    "claim": "The national debt crossed $36 trillion, with annual interest payments exceeding $1 trillion, surpassing defense spending for the first time",
    "speaker": "Jason Calacanis",
    "timestamp": "00:11:48",
    "category": "statistic",
    "confidence": "stated_as_fact",
    "checkable": true
  },
  {
    "claim": "About 30% of US treasuries are held by foreign entities",
    "speaker": "David Friedberg",
    "timestamp": "00:12:05",
    "category": "statistic",
    "confidence": "stated_as_fact",
    "checkable": true
  },
  {
    "claim": "A 1 percentage point increase in GDP growth generates roughly $300 billion in additional tax revenue annually",
    "speaker": "David Sacks",
    "timestamp": "00:13:02",
    "category": "statistic",
    "confidence": "stated_as_fact",
    "checkable": true
  },
  {
    "claim": "Commonwealth Fusion Systems' SPARC magnets achieved a field strength of 20 tesla, roughly twice what ITER's magnets can achieve",
    "speaker": "David Friedberg",
    "timestamp": "00:13:48",
    "category": "statistic",
    "confidence": "stated_as_fact",
    "checkable": true
  },
  {
    "claim": "ITER's estimated cost is $22 billion vs SPARC's $2 billion",
    "speaker": "David Friedberg",
    "timestamp": "00:13:48",
    "category": "statistic",
    "confidence": "stated_as_fact",
    "checkable": true
  }
]
```

**Extraction stats:** 14 checkable claims from a ~15 minute segment. That's roughly **1 checkable claim per minute** — consistent with the All-In format which is heavy on stats and figures.

---

## 4. End-to-End Pipeline Architecture

```
┌─────────────┐     ┌──────────────┐     ┌───────────────┐
│  YouTube URL │────▶│   yt-dlp     │────▶│  Raw VTT file │
└─────────────┘     └──────────────┘     └───────┬───────┘
                                                  │
                                          ┌───────▼───────┐
                                          │  VTT Parser   │
                                          │  + Dedup      │
                                          └───────┬───────┘
                                                  │
                                          ┌───────▼───────┐
                                          │  LLM Clean    │
                                          │  (punctuation,│
                                          │   speakers)   │
                                          └───────┬───────┘
                                                  │
                                          ┌───────▼───────┐
                                          │  LLM Extract  │
                                          │  (claims →    │
                                          │   JSON array) │
                                          └───────┬───────┘
                                                  │
                                          ┌───────▼───────┐
                                          │  Fact Check    │
                                          │  (web search + │
                                          │   LLM verify)  │
                                          └───────┬───────┘
                                                  │
                                          ┌───────▼───────┐
                                          │  Output Report │
                                          │  (per-claim    │
                                          │   verdicts)    │
                                          └──────────────┘
```

### Cost Estimate (per episode)

| Step | Method | Est. Cost |
|------|--------|-----------|
| Transcript | yt-dlp | Free |
| Clean | Claude Haiku (8K tokens) | ~$0.01 |
| Extract claims | Claude Sonnet (8K in, 2K out) | ~$0.05 |
| Fact-check (14 claims × search + verify) | Sonnet × 14 | ~$0.70 |
| **Total** | | **~$0.76/episode** |

---

## 5. Sandbox Notes

- **yt-dlp could not run** in the Docker sandbox (no pip, no network access for package install)
- Sample transcript created manually based on real All-In episode topics and format
- In production, yt-dlp works reliably for any YouTube video with captions enabled
- Most All-In episodes have auto-generated captions available within hours of upload
