# PodCheck Data Pipeline Strategy

> Brutally practical blueprint for ingesting podcasts, extracting claims, and verifying them at scale.

---

## 1. Podcast Discovery

### The Landscape

- **~4.4M podcasts** exist globally (PodcastIndex count as of early 2026)
- **~100-120M total episodes** published
- Only ~500K podcasts are "active" (released an episode in last 30 days)
- Only ~50K have meaningful audiences (>1K downloads/episode)
- Only ~5K are claim-dense enough to be interesting for PodCheck

### Discovery APIs

| API | Cost | Coverage | Best For |
|-----|------|----------|----------|
| **PodcastIndex** | Free, open | ~4.4M podcasts, full RSS | Primary discovery, episode feeds, metadata |
| **Apple Podcasts API** | Free (Search API) | ~2.5M podcasts | Charts/rankings as popularity proxy |
| **Spotify Web API** | Free (with auth) | ~5M podcasts | Show metadata, episode lists |
| **Listen Notes** | Free tier: 300 req/mo; $29/mo for 6K | ~3.3M podcasts | Search, genre classification, audience estimates |
| **Chartable/Podtrac** | Paid/partnership | Top charts | Audience size estimates |

### Recommended Stack

**Primary:** PodcastIndex for discovery + RSS feed monitoring. It's free, open, and has the best episode-level metadata including `enclosureUrl` (the actual audio file).

**Supplement with:** Apple Charts API to estimate popularity tiers. Listen Notes for search and genre tagging.

### Prioritization Formula

```
priority_score = (audience_tier × 3) + (claim_density_estimate × 5) + (topic_diversity × 2)
```

**Audience tier** (1-5): Estimated from chart positions, social followers, review counts.
- Tier 5: >500K downloads/ep (JRE, NPR shows)
- Tier 4: 100K-500K
- Tier 3: 25K-100K
- Tier 2: 5K-25K
- Tier 1: <5K

**Claim density estimate** (1-5): Based on genre/format.
- 5: News analysis, science, health, economics, politics (interview format)
- 4: Business, tech commentary, history
- 3: True crime, investigative journalism
- 2: Culture, comedy with commentary
- 1: Fiction, music, pure entertainment

**Topic diversity** (1-3): Bonus for underrepresented topics in our database.

### Feed Monitoring

- Poll RSS feeds of tracked podcasts every 6 hours via PodcastIndex
- PodcastIndex also offers a WebSocket for real-time new episode alerts (use in Phase 2+)
- Store: podcast_id, title, feed_url, last_checked, priority_score, active_status

---

## 2. Transcript Sources (Ranked by Cost-Effectiveness)

### Source Comparison

#### 1. YouTube Auto-Captions — FREE, Primary Source
- **Coverage:** ~60-70% of major podcasts publish video versions on YouTube
- **Quality:** 85-92% word accuracy; degrades with accents, crosstalk, technical jargon
- **Access:** `yt-dlp --write-auto-sub --sub-lang en --skip-download` — no API key needed
- **Pros:** Free, timestamped, immediate availability
- **Cons:** No speaker labels, garbles proper nouns and numbers, occasionally missing
- **Rate limits:** YouTube throttles aggressive scraping. Use rotating IPs or modest pacing (10-20/hour is safe)
- **Critical flaw for us:** Numbers and names are frequently wrong. "2.3 million" becomes "2.3 billion." Must flag numeric claims from YouTube captions for extra verification.

#### 2. Spotify Transcripts — FREE-ish, Secondary Source
- **Coverage:** Spotify auto-generates transcripts for most hosted shows (~70%+ of catalog)
- **Access:** Unofficial — requires reverse-engineering Spotify's internal API or using `spotify-podcast-transcripts` type tools
- **Quality:** Comparable to YouTube, ~88-93% accuracy
- **Pros:** Free, often has basic speaker separation
- **Cons:** Legal grey area, API could break anytime, not all shows
- **Recommendation:** Use as supplementary source; don't depend on it

#### 3. Whisper (Self-Hosted) — ~$0.001/min, Primary Fallback
- **Model:** `whisper-large-v3` via `faster-whisper` (CTranslate2 backend)
- **Hardware:** Single RTX 4090 processes ~30x realtime (a 60-min episode in ~2 min)
- **Cost breakdown:**
  - GPU rental: ~$0.40/hr (RunPod, vast.ai) → $0.013 per 60-min episode
  - Or own hardware: amortized ~$0.001/min after initial $1,600 GPU cost
- **Quality:** 93-97% accuracy, best open-source option
- **Pros:** Full control, can batch process, excellent with `--word_timestamps`
- **Cons:** Requires GPU infrastructure, no built-in diarization (pair with pyannote)
- **Setup:** Docker container with `faster-whisper` + CUDA, queue via Redis/BullMQ

#### 4. Deepgram API — ~$0.006/min
- **Tier:** Nova-2 model
- **Quality:** 90-95%, very fast (~10x realtime)
- **Pros:** Built-in diarization, keyword boosting (great for fixing proper nouns), streaming support
- **Cons:** Costs add up at scale
- **When to use:** When we need fast turnaround + diarization and don't have GPU capacity

#### 5. AssemblyAI — ~$0.01/min
- **Quality:** 91-96%, best built-in speaker diarization
- **Pros:** Speaker labels, entity detection, sentiment — lots of features out of the box
- **Cons:** Most expensive of the automated options
- **When to use:** High-priority episodes where diarization accuracy is critical

#### 6. Official Transcripts — FREE, Gold Standard
- **Coverage:** <5% of podcasts publish transcripts (some NPR shows, a few independents)
- **Access:** Podcast RSS `<podcast:transcript>` tag, show websites
- **Quality:** 99%+ when available
- **Pros:** Perfect accuracy, often has speaker labels
- **Cons:** Extremely rare

### MVP Transcript Strategy

```
1. Check for official transcript (RSS <podcast:transcript> tag) → use if available
2. Check YouTube for video version → yt-dlp auto-captions
3. If no YouTube: download audio from RSS enclosure → Whisper self-hosted
4. Store all transcripts with source quality flag: official|youtube|whisper|deepgram
```

**Expected source distribution for MVP (top 15 podcasts):**
- YouTube captions: ~11 podcasts (73%)
- Whisper fallback: ~3 podcasts (20%)
- Official transcript: ~1 podcast (7%)

---

## 3. Speaker Diarization

### The Problem

We need to know WHO said WHAT. A transcript that says "crime is up 40%" is useless without knowing if the host or guest said it.

### Approach by Source

**YouTube captions:** No speaker labels. Must add diarization.
**Whisper:** No speaker labels. Must add diarization.
**Deepgram/AssemblyAI:** Speaker labels included (Speaker 0, Speaker 1, etc.) but NOT named.
**Official transcripts:** Usually has names. Gold.

### Diarization Pipeline

#### Step 1: Segment by Speaker — pyannote.audio

- **Model:** `pyannote/speaker-diarization-3.1`
- **Accuracy:** 85-95% DER (Diarization Error Rate) depending on audio quality
- **Performance:** ~5x realtime on CPU, ~50x on GPU
- **Cost:** Free (open-source, MIT license), just compute
- **Output:** Timestamped speaker segments: `[0:00-0:45] SPEAKER_00`, `[0:45-1:12] SPEAKER_01`

```python
from pyannote.audio import Pipeline
pipeline = Pipeline.from_pretrained("pyannote/speaker-diarization-3.1")
diarization = pipeline("episode.wav")
for turn, _, speaker in diarization.itertracks(yield_label=True):
    print(f"[{turn.start:.1f} - {turn.end:.1f}] {speaker}")
```

#### Step 2: Map Speaker IDs to Names

pyannote gives us `SPEAKER_00`, `SPEAKER_01`. We need "Joe Rogan" and "Dr. Sarah Chen."

**Method A — Metadata extraction (primary):**
1. Parse episode title, description, show notes for guest names
2. Use LLM to extract: "Who is the guest on this episode?" from show notes
3. Host is known (stored in podcast metadata). Guest is extracted.
4. If 2 speakers detected and we know host + guest → direct mapping
5. Use first 2 minutes of audio (host usually introduces themselves and guest)

**Method B — LLM transcript analysis:**
- Feed first 5 minutes of diarized transcript to LLM
- Prompt: "Based on this conversation, identify which speaker is the host and which is the guest. The host is [name]. Who is SPEAKER_00 and SPEAKER_01?"
- Works ~90% of the time for 2-person shows

**Method C — Voice fingerprinting (Phase 3+):**
- Build speaker embeddings using `pyannote/embedding` or `resemblyzer`
- Store host voice embeddings (they appear in every episode)
- Auto-match: "This voice matches Joe Rogan's stored embedding"
- Expensive to build, but very accurate once operational

#### Step 3: Person Entity Database

```sql
CREATE TABLE persons (
    id UUID PRIMARY KEY,
    canonical_name TEXT NOT NULL,       -- "Andrew Huberman"
    aliases TEXT[],                      -- ["Huberman", "Dr. Huberman", "Andrew D. Huberman"]
    wikipedia_url TEXT,
    twitter_handle TEXT,
    role TEXT,                           -- "host" | "guest" | "both"
    primary_podcast_id UUID,            -- if they're a host
    bio_summary TEXT,
    claim_count INT DEFAULT 0,
    accuracy_score FLOAT,
    created_at TIMESTAMP
);

CREATE TABLE appearances (
    person_id UUID REFERENCES persons(id),
    episode_id UUID REFERENCES episodes(id),
    role TEXT,                           -- "host" | "guest" | "co-host"
    speaker_label TEXT,                  -- "SPEAKER_00"
    confidence FLOAT
);
```

- Deduplicate across podcasts: "Dr. Peter Attia" on Huberman Lab = "Peter Attia" on Tim Ferriss = same person
- Use Wikidata QIDs as canonical identifiers where available
- LLM-assisted entity resolution for ambiguous cases

### Diarization Accuracy Targets

| Scenario | Expected Accuracy | Notes |
|----------|------------------|-------|
| 2 speakers, clean audio | 92-97% | Most podcast interviews |
| 2 speakers, crosstalk | 80-90% | Heated debates |
| 3+ speakers | 75-88% | Panel shows, harder |
| Phone-in guests | 85-93% | Different audio quality helps separation |

**For MVP:** Only target 2-speaker shows (host + guest). This covers ~80% of claim-dense podcasts. Multi-speaker panels are Phase 2.

---

## 4. Claim Extraction Pipeline

### Claim Taxonomy

**IS a verifiable claim:**

| Type | Example | Verification Method |
|------|---------|-------------------|
| **Statistical** | "Unemployment is at 3.7%" | BLS data lookup |
| **Historical** | "The Roman Empire fell in 476 AD" | Reference check |
| **Scientific** | "mRNA vaccines alter your DNA" | PubMed, expert consensus |
| **Causal** | "Minimum wage increases cause unemployment" | Economic literature review |
| **Predictive** | "Inflation will hit 5% by Q3" | Calendar queue → re-evaluate |
| **Attribution** | "Einstein said insanity is doing the same thing..." | Quote databases |
| **Quantitative** | "The US has 330 million people" | Census data |

**NOT a verifiable claim (filter out):**

- Opinions: "I think chocolate is the best flavor"
- Jokes/sarcasm: "Oh sure, the moon is made of cheese"
- Hypotheticals: "If we colonized Mars..."
- Vague statements: "Things are getting worse"
- Personal anecdotes: "When I was in college..."
- Predictions too vague to verify: "AI will change everything"
- Common knowledge: "Water is wet"

### Extraction Prompt Engineering

```
System: You are a claim extraction engine for podcast transcripts. Extract 
ONLY verifiable factual claims — statements that can be checked against 
objective data sources.

For each claim, output JSON:
{
  "claim_text": "exact quote from transcript",
  "speaker": "speaker name",
  "timestamp": "HH:MM:SS",
  "claim_type": "statistical|historical|scientific|causal|predictive|attribution|quantitative",
  "verification_difficulty": "easy|medium|hard",
  "key_entities": ["entity1", "entity2"],
  "context": "1-2 sentences of surrounding context"
}

Rules:
- Only extract claims that a fact-checker could verify with public data
- Include the EXACT quote, not a paraphrase
- Skip opinions, jokes, hypotheticals, personal anecdotes
- When uncertain if something is a claim, err on the side of inclusion
- For predictions, note what would need to be true and by when
```

**Processing approach:**
- Split transcript into ~3,000 token chunks with 200-token overlap
- Process each chunk independently (parallelizable)
- Deduplicate claims that span chunk boundaries
- One final pass to merge and rank claims

### Volume Estimates

| Podcast Type | Claims/Hour | Examples |
|-------------|-------------|----------|
| News/politics interview | 30-45 | Pod Save America, The Daily |
| Science/health | 25-40 | Huberman Lab, Peter Attia |
| Economics/business | 20-35 | All-In, Planet Money |
| History | 15-25 | Hardcore History, Revolutions |
| Comedy with commentary | 5-15 | JRE (varies wildly by guest) |
| True crime | 10-20 | Serial, Crime Junkie |

**Average across claim-dense podcasts: ~25 claims per hour.**

### Cost Per Episode (Extraction Only)

Using Claude 3.5 Sonnet (best cost/quality for extraction):

- Average episode: ~60 minutes → ~12,000 words → ~15,000 tokens input
- With chunking + system prompt overhead: ~20,000 input tokens per episode
- Output: ~3,000 tokens (25 claims × ~120 tokens each)
- **Claude 3.5 Sonnet pricing:** $3/M input, $15/M output
- Cost: (20K × $3/M) + (3K × $15/M) = $0.06 + $0.045 = **~$0.10 per episode**
- With retries, validation pass, edge cases: **~$0.15-0.30 per episode**

**Cheaper alternative:** Use Claude 3.5 Haiku for first pass ($0.25/M input, $1.25/M output) → ~$0.01/episode, then Sonnet only for ambiguous claims. Blended cost: ~$0.05/episode.

---

## 5. Fact Verification Sources

### Source Hierarchy (by reliability)

#### Tier 1 — Authoritative Government/Institutional Data
| Source | Covers | Access | Rate Limits |
|--------|--------|--------|-------------|
| **BLS (Bureau of Labor Statistics)** | Employment, inflation, wages, CPI | Free API | 500 req/10min |
| **BEA (Bureau of Economic Analysis)** | GDP, trade, personal income | Free API | Generous |
| **Census Bureau** | Population, demographics | Free API | Generous |
| **Federal Reserve (FRED)** | 800K+ economic time series | Free API key | 120 req/min |
| **WHO** | Global health statistics | Free API | Moderate |
| **CDC WONDER** | US health/mortality data | Free | Bulk download |
| **World Bank Open Data** | Global development indicators | Free API | Generous |
| **IMF Data** | Fiscal, monetary, trade data | Free API | Generous |

#### Tier 2 — Academic & Scientific
| Source | Covers | Access | Notes |
|--------|--------|--------|-------|
| **PubMed/NCBI** | Biomedical literature | Free API (E-utilities) | Check systematic reviews first |
| **Google Scholar** | Broad academic | Scraping (no official API) | Use SerpAPI ($50/mo) as proxy |
| **Semantic Scholar** | CS, bio, medicine | Free API | Good for citation counts |
| **Cochrane Library** | Medical evidence reviews | Paid, some free | Gold standard for health claims |

#### Tier 3 — Fact-Check Aggregators
| Source | Covers | Access | Notes |
|--------|--------|--------|-------|
| **Google Fact Check API** | ClaimReview markup from fact-checkers | Free API | Aggregates Snopes, PolitiFact, etc. |
| **ClaimBuster** | Claim detection & matching | Free API | Academic project, useful for matching |
| **Full Fact API** | UK-focused fact checks | Free | Good for international claims |

#### Tier 4 — Reference & General
| Source | Covers | Access | Notes |
|--------|--------|--------|-------|
| **Wikipedia/Wikidata** | General knowledge, structured data | Free API | Good for historical, biographical |
| **Wolfram Alpha** | Computational facts | API ($7.50/mo for 2K queries) | Great for math/science claims |
| **News archives** | Current events | Various (NewsAPI $449/mo for commercial) | For recent event verification |

### Verification Pipeline

```
claim → classify type → route to appropriate sources → gather evidence → LLM synthesis → verdict

Verdicts:
- TRUE: Supported by authoritative sources
- MOSTLY TRUE: Substantially correct, minor inaccuracies
- HALF TRUE: Partially correct, missing important context
- MOSTLY FALSE: Contains some truth but misleading
- FALSE: Contradicted by authoritative sources
- UNVERIFIABLE: Cannot find sufficient evidence
- PREDICTION: Queued for future verification (with date)
```

**LLM Verification Prompt (final synthesis):**
```
Given this claim and the evidence gathered, provide a verdict.

Claim: "[claim text]"
Speaker: [name]
Context: [surrounding discussion]

Evidence gathered:
[source 1]: [finding]
[source 2]: [finding]
...

Provide:
1. Verdict: TRUE | MOSTLY_TRUE | HALF_TRUE | MOSTLY_FALSE | FALSE | UNVERIFIABLE
2. Confidence: 0.0-1.0
3. Explanation: 2-3 sentences
4. Sources: URLs
5. Nuance: Any important context the speaker omitted
```

**Cost per claim verification:**
- API calls to data sources: ~$0.001 (mostly free APIs)
- LLM synthesis (Claude Sonnet): ~$0.01-0.03
- **Total per claim: ~$0.02-0.04**
- **Per episode (25 claims): ~$0.50-1.00**

### Prediction Queue

For predictive claims ("Bitcoin will hit $100K by December"):
- Store with `prediction_deadline` date
- Cron job checks predictions weekly
- When deadline passes: auto-gather evidence, generate verdict
- Special UI badge: "Prediction — awaiting outcome"

---

## 6. Cost Modeling

### Per-Episode Cost Breakdown

| Component | Low Est. | High Est. | Notes |
|-----------|----------|-----------|-------|
| Transcript (YouTube) | $0.00 | $0.00 | Free |
| Transcript (Whisper fallback) | $0.01 | $0.02 | GPU rental |
| Diarization (pyannote) | $0.01 | $0.02 | CPU compute |
| Claim extraction (LLM) | $0.10 | $0.30 | Claude API |
| Fact verification (sources + LLM) | $0.30 | $1.50 | Depends on claim count |
| Storage & infra | $0.02 | $0.05 | DB, object storage |
| **Total per episode** | **$0.44** | **$1.89** | |

**Realistic average: ~$0.80/episode** for a typical 60-minute claim-dense podcast.

### Monthly Scaling Costs

| Scale | Episodes/mo | Pipeline Cost | Infra | Total/mo |
|-------|-------------|--------------|-------|----------|
| **MVP (Phase 1)** | 60-80 | $50-150 | $20 (Supabase free + small VPS) | **$70-170** |
| **Growth (Phase 2)** | 500-1,000 | $400-1,500 | $100-300 (dedicated GPU, bigger DB) | **$500-1,800** |
| **Scale (Phase 3)** | 2,000-5,000 | $1,600-7,500 | $500-1,000 | **$2,100-8,500** |
| **Full (Phase 4)** | 10,000+ | Self-hosted models critical | $2,000-5,000 | **$3,000-8,000** |

### Cost Optimization Levers

1. **Haiku-first extraction:** Use Claude Haiku for initial claim detection, Sonnet only for verification synthesis → 60% cost reduction on extraction
2. **Claim caching:** Same claim across episodes (e.g., "US debt is $34 trillion") → verify once, reuse
3. **Self-hosted LLMs at scale:** Llama 3.1 70B for extraction at Phase 3+ → near-zero marginal cost
4. **Batch API pricing:** Claude Batch API is 50% cheaper, acceptable for non-realtime processing
5. **Skip low-density episodes:** If first chunk yields <3 claims, skip rest of episode

---

## 7. Seeding Strategy — Top 15 Podcasts

### Selection Criteria Applied

Each podcast scored on: **Claim Density (CD)**, **Audience Size (AS)**, **Transcript Availability (TA)**, **Topic Diversity for portfolio (TD)**, **Controversy/Interest (CI)**.

| # | Podcast | Why | CD | AS | TA | Format |
|---|---------|-----|----|----|-----|--------|
| 1 | **The Joe Rogan Experience** | Largest audience, massive claim variation by guest, YouTube available | 3 | 5 | ✅ YT | Long-form interview |
| 2 | **Huberman Lab** | Extremely claim-dense health/neuroscience, huge audience, YouTube | 5 | 5 | ✅ YT | Solo + interview |
| 3 | **All-In Podcast** | Tech/economics/politics claims, influential audience, YouTube | 5 | 4 | ✅ YT | Panel (4 speakers — good test) |
| 4 | **Lex Fridman Podcast** | Science/tech/philosophy claims, YouTube, long-form | 4 | 5 | ✅ YT | Long-form interview |
| 5 | **The Peter Attia Drive** | Dense medical/longevity claims, passionate audience | 5 | 4 | ✅ YT | Interview |
| 6 | **Pod Save America** | Political claims, weekly cadence, high volume | 4 | 4 | ✅ YT | Panel commentary |
| 7 | **The Daily (NYT)** | News claims, daily cadence, massive audience | 4 | 5 | ✅ Official | News narrative |
| 8 | **Freakonomics Radio** | Economics/stats claims, well-researched (good calibration benchmark) | 4 | 4 | ✅ Official | Produced narrative |
| 9 | **Ben Shapiro Show** | Political claims, high controversy, daily | 4 | 5 | ✅ YT | Solo commentary |
| 10 | **The Tim Ferriss Show** | Health/business claims, diverse guests | 3 | 4 | ✅ YT | Long-form interview |
| 11 | **Radiolab** | Science claims, good for testing narrative format extraction | 3 | 4 | ✅ Official | Produced narrative |
| 12 | **Planet Money (NPR)** | Economics claims, short episodes (good for testing) | 4 | 4 | ✅ Official | Produced narrative |
| 13 | **The Megyn Kelly Show** | Political/cultural claims, controversial, YouTube | 4 | 4 | ✅ YT | Interview + commentary |
| 14 | **Acquired** | Business history claims, very detailed, long episodes | 4 | 4 | ✅ YT | Narrative + discussion |
| 15 | **Found My Fitness (Rhonda Patrick)** | Dense biomedical claims, supplement/nutrition focus | 5 | 3 | ✅ YT | Interview + solo |

### Why This Mix

- **Topic coverage:** Health/science (3), politics (3), economics (3), tech/business (3), general interest (3)
- **Format coverage:** Long-form interview (6), panel (2), solo commentary (1), produced narrative (4), mixed (2)
- **Transcript sources:** YouTube (11), official transcripts (4) — tests both pipelines
- **Political spectrum:** Left-leaning (2), right-leaning (2), centrist/apolitical (11) — avoids bias perception at launch
- **Controversy gradient:** High-controversy (JRE, Shapiro, Kelly) to low (Planet Money, Radiolab) — tests both audience reactions
- **Episode cadence:** Daily (3), weekly (10), bi-weekly (2) — manageable volume of ~30-40 episodes/week

### Initial Volume

15 podcasts × ~2-3 episodes/week average = **~35-45 new episodes/week** = ~150-180/month

At $0.80/episode average = **~$120-145/month pipeline cost** for MVP.

---

## 8. Data Quality

### Transcript Accuracy Issues

**The Numbers Problem:**
YouTube auto-captions frequently mangle numbers. "2.3 million" → "2.3 billion", "$400" → "for hundred dollars". This is catastrophic for a fact-checking product.

**Mitigation:**
1. Flag all numeric claims from YouTube captions as `low_confidence_transcript`
2. Cross-reference numbers against episode show notes (guests often share stats)
3. For critical numeric claims, re-transcribe that segment with Whisper
4. Build a "number sanity checker" — if a claim says US population is 3.3 billion, auto-flag

**The Names Problem:**
"Dr. Fauci" → "Dr. Fouchy", "Elon Musk" → "Ilan Musk". Proper noun errors are ~5-10% of words in YouTube captions.

**Mitigation:**
1. Build a known-entity dictionary (people, organizations, drugs, studies)
2. Fuzzy-match transcript names against dictionary
3. Use episode metadata (guest name is usually spelled correctly in title/description)

### Version Control for Claims & Scores

```sql
CREATE TABLE claim_versions (
    claim_id UUID,
    version INT,
    verdict TEXT,
    confidence FLOAT,
    evidence JSONB,
    verified_by TEXT,          -- "auto" | "human_reviewer"
    created_at TIMESTAMP,
    superseded_at TIMESTAMP,   -- NULL if current
    reason_for_update TEXT     -- "new evidence" | "correction" | "source retraction"
);
```

- Every claim verdict is immutable once written; updates create new versions
- Public-facing shows current verdict + "Previously rated X on [date]" if changed
- Full audit trail accessible via API

### Correction & Retraction Handling

1. **Speaker issues correction:** If a podcaster corrects themselves in a later episode, link the correction to the original claim. Update verdict. Show both.
2. **Source data revised:** Government stats get revised (GDP, jobs numbers). Flag affected claims, re-verify, note "based on revised data."
3. **User disputes:** Allow flagging. Queue for human review. If overturned, new version with explanation.
4. **Automated re-checks:** Monthly cron re-verifies claims tagged `low_confidence` or with time-sensitive sources.

### Quality Metrics to Track

- **Transcript WER** (Word Error Rate): Sample and manually check 5% of transcripts
- **Claim precision:** What % of extracted "claims" are actually verifiable? Target: >85%
- **Claim recall:** What % of verifiable claims in a transcript do we catch? Target: >70%
- **Verdict accuracy:** Spot-check against professional fact-checkers. Target: >80% agreement
- **Inter-rater reliability:** When two LLM runs disagree, flag for human review

---

## 9. Scaling Plan

### Phase 1: Foundation (Months 1-3) — Semi-Manual

**Scope:** 15-20 podcasts, ~150 episodes/month

**Infrastructure:**
- Single VPS ($20/mo) running transcript pipeline
- Supabase free tier for database
- Claude API for extraction + verification
- Manual review of all verdicts before publishing

**Pipeline:**
```
YouTube captions (yt-dlp) → pyannote diarization → Claude extraction → 
Claude verification → Human review queue → Publish
```

**Team:** 1 developer, part-time reviewer (or founder doing reviews)

**Cost:** ~$150-250/month total (API + hosting)

**Goals:**
- Validate claim extraction quality
- Build initial person database (~200 people)
- Establish verdict accuracy baseline
- Launch with ~500-1,000 verified claims
- Get user feedback on what matters

**Key decisions to make:**
- Verdict scale (binary true/false vs. 6-point scale vs. numeric score?)
- How to display uncertainty?
- What's the minimum confidence to publish?

### Phase 2: Automation (Months 4-8) — Top 500 Podcasts

**Scope:** 500 podcasts, ~2,000 episodes/month

**Infrastructure upgrades:**
- Dedicated GPU instance for Whisper ($200-400/mo on RunPod)
- Managed Postgres (Supabase Pro or RDS, ~$25-50/mo)
- Redis queue for pipeline orchestration
- Monitoring & alerting (Grafana, ~$0)

**Pipeline additions:**
- Whisper for podcasts without YouTube
- Automated RSS polling every 6 hours
- Claim deduplication (same stat cited by 5 podcasts → verify once)
- Human review only for low-confidence verdicts (<0.7)
- Batch Claude API for 50% cost savings

**Cost:** ~$1,000-2,500/month

**Goals:**
- Process new episodes within 24 hours of release
- Build person profiles for ~2,000 people
- Claim dedup database for common statistics
- 90%+ of verdicts fully automated

### Phase 3: Scale (Months 9-15) — 2,000+ Podcasts

**Scope:** 2,000-5,000 podcasts, ~10,000 episodes/month

**Infrastructure upgrades:**
- Self-hosted Llama 3.1 70B for claim extraction (2× A100 GPUs, ~$2,000/mo)
- Keep Claude for verification synthesis only (higher accuracy needed)
- Kubernetes or similar for pipeline orchestration
- CDN for transcript/claim data serving
- Voice fingerprint database for hosts

**Pipeline additions:**
- Real-time processing via PodcastIndex WebSocket
- Voice fingerprinting for automatic speaker identification
- Cross-podcast claim linking ("Dr. X said this on 3 different shows")
- Trending claims dashboard (what claims are going viral?)
- API for third-party integrations

**Cost:** ~$4,000-8,000/month

**Goals:**
- New episodes processed within 6 hours of release
- 10,000+ person profiles
- <5% of verdicts need human review
- Public API launched

### Phase 4: Universal (Months 16+) — Any Podcast

**Scope:** Any podcast, on-demand + automated top 10K

**Infrastructure:**
- Fully self-hosted ML pipeline (extraction, verification, diarization)
- Multi-region deployment for latency
- User-submitted podcast processing queue

**New features:**
- "Check this podcast" — users submit any RSS feed
- Real-time during-listen fact overlay (browser extension / app integration)
- Guest pre-show briefing: "Here's what [guest] has claimed before and their accuracy score"
- Podcast comparison: "Show A's claims are 87% accurate vs Show B's 62%"
- Weekly email digests of biggest misstatements

**Cost:** ~$8,000-15,000/month at scale (mostly compute)

**Revenue needed to sustain:** Achievable with ~2,000 paying subscribers at $10/mo or ad-supported model at ~100K MAU.

---

## Appendix: Tech Stack Summary

| Component | MVP Choice | Scale Choice |
|-----------|-----------|--------------|
| **Language** | TypeScript (Next.js full-stack) | TypeScript + Python (ML pipeline) |
| **Database** | Supabase (Postgres) | Managed Postgres + Redis |
| **Queue** | BullMQ (Redis) | BullMQ or Temporal |
| **Transcription** | yt-dlp + faster-whisper | Self-hosted Whisper cluster |
| **Diarization** | pyannote.audio | pyannote + voice fingerprinting |
| **Claim extraction** | Claude 3.5 Sonnet API | Self-hosted Llama 3.1 70B + Claude |
| **Verification** | Claude + free data APIs | Claude + comprehensive source network |
| **Hosting** | Single VPS + Vercel | Kubernetes on Hetzner/AWS |
| **Monitoring** | Console logs + Sentry | Grafana + Prometheus + custom dashboards |

---

*Last updated: 2026-02-07. This is a living document — update as we learn from real pipeline data.*
