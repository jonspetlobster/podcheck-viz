# PodCheck — Technical Plan & Architecture

## 1. Product Vision & Strategy

### Mission
Build the definitive accuracy record for every public voice in podcasting. When someone makes a claim on a podcast, it gets tracked, verified, and scored — creating an accountable, searchable, evolving record of who's right and who's full of it.

### Target Users
- **Casual listeners** — "Should I trust what this guy just said?"
- **Journalists** — Vetting podcast guests before booking or citing
- **PR/Booking agents** — Checking a client's public accuracy record
- **Researchers/Academics** — Studying misinformation, media literacy
- **Podcast hosts** — Checking guest credibility before booking
- **The guests themselves** — Reputation management

### Competitive Moat
- **Data compound effect** — Every day of claims processed makes the dataset more valuable. First mover builds an insurmountable lead.
- **Network effects** — More users → more dispute/verification input → better accuracy → more trust → more users
- **Brand as arbiter** — Becoming the "credit score" for podcast credibility is a winner-take-most market

### Revenue Model (see BUSINESS.md for detail)
- Freemium consumer access
- B2B API (media companies, PR firms, booking platforms)
- Premium analytics dashboards
- Data licensing

---

## 2. Core Architecture

### High-Level System Design

```
┌─────────────────────────────────────────────────────────┐
│                     INGESTION LAYER                      │
│  Podcast Discovery → Episode Fetcher → Transcription     │
│  (PodcastIndex API)  (RSS/YouTube)    (Whisper/Deepgram) │
└──────────────────────┬──────────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────────┐
│                    PROCESSING LAYER                      │
│  Speaker Diarization → Claim Extraction → Classification │
│  (pyannote/NeMo)      (GPT-4/Claude)    (Category/Type) │
└──────────────────────┬──────────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────────┐
│                   VERIFICATION LAYER                     │
│  Source Matching → Fact Check → Scoring → Human Review   │
│  (RAG pipeline)   (LLM + DB)  (Algorithm)  (Queue)      │
└──────────────────────┬──────────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────────┐
│                     DATA LAYER                           │
│  PostgreSQL (core) │ Elasticsearch (search) │ Redis      │
│  S3 (audio/transcripts) │ ClickHouse (analytics)        │
└──────────────────────┬──────────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────────┐
│                      API LAYER                           │
│  REST API │ GraphQL │ WebSocket (live updates)           │
│  Rate limiting │ Auth │ API keys                         │
└──────────────────────┬──────────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────────┐
│                    FRONTEND LAYER                         │
│  Next.js │ SSR/SSG │ CDN │ PWA                           │
│  Search │ Profiles │ Claims │ Dashboards                 │
└─────────────────────────────────────────────────────────┘
```

### Data Pipeline Flow

1. **Discover** — Scan PodcastIndex/Apple/Spotify for podcasts and episodes
2. **Fetch** — Download audio or grab existing transcripts (YouTube captions, Spotify transcripts)
3. **Transcribe** — Whisper large-v3 for audio without transcripts (~$0.006/min via API, ~$0.001/min self-hosted)
4. **Diarize** — Speaker diarization to identify who said what (pyannote.audio)
5. **Identify** — Match speaker segments to known people (episode metadata + voice fingerprint)
6. **Extract** — LLM pass to extract verifiable claims from each speaker's segments
7. **Classify** — Categorize claims (economics, science, politics, etc.) and type (factual, prediction, causal, statistical)
8. **Verify** — Check claims against authoritative sources via RAG pipeline
9. **Score** — Apply scoring algorithm, generate confidence levels
10. **Review** — Queue uncertain claims for human review
11. **Publish** — Update person/podcast profiles, trigger notifications

---

## 3. AI/ML Pipeline

### Claim Extraction

**What counts as a claim:**
- Factual assertions: "GDP grew 3% last quarter"
- Predictions: "Inflation will hit 5% by December"
- Causal claims: "The tariffs caused the recession"
- Statistical claims: "90% of startups fail"
- Historical claims: "The US has never defaulted on its debt"
- Scientific claims: "mRNA vaccines don't alter DNA"

**What doesn't count:**
- Pure opinions: "I think Tesla makes the best cars"
- Jokes/sarcasm (detected via context)
- Hypotheticals: "If we did X, then Y might happen"
- Vague statements: "The economy is doing well"
- Questions

**Extraction approach:**
- GPT-4o or Claude for claim extraction (best accuracy/cost ratio)
- Custom prompt with few-shot examples and claim taxonomy
- Estimated yield: 15-40 verifiable claims per hour of podcast content
- Post-extraction deduplication (same claim made multiple times)

### Scoring Methodology

**Per-claim scoring (1-5 scale):**
| Score | Label | Definition |
|-------|-------|-----------|
| 5 | Verified | Claim is accurate per authoritative sources |
| 4 | Mostly True | Substantially correct with minor inaccuracies |
| 3 | Mixed | Contains elements of truth and falsehood |
| 2 | Mostly False | Substantially incorrect with minor truths |
| 1 | False | Claim is demonstrably wrong |
| 0 | Unverifiable | Cannot be checked (yet) |
| P | Pending | Prediction — not yet resolvable |

**Per-person aggregate scoring:**
- Weighted average of all scored claims
- Weighted by: claim importance, recency, confidence of verification
- Broken down by domain (economics, science, politics, tech, etc.)
- Tracked over time (rolling 90-day, yearly, all-time)
- Minimum claim threshold before publishing a score (≥10 scored claims)

**Confidence levels on each verification:**
- High: Multiple authoritative sources agree
- Medium: Some sources support, context dependent
- Low: Limited sources, interpretation required

### Handling Predictions
- Tag predictions with expected resolution date
- Queue for re-evaluation when date arrives
- Track prediction accuracy separately ("forecasting score")
- This is a killer feature — no one else tracks this systematically

### Bias Mitigation
- Multi-source verification (never rely on one source)
- Political claim scoring reviewed by diverse human panel
- Transparency: all sources shown, methodology public
- Appeals process for every score
- Regular bias audits of the AI pipeline

---

## 4. Database Schema (Core Entities)

### PostgreSQL Schema

```sql
-- Podcasts
CREATE TABLE podcasts (
  id UUID PRIMARY KEY,
  name TEXT NOT NULL,
  slug TEXT UNIQUE NOT NULL,
  description TEXT,
  rss_url TEXT,
  apple_id TEXT,
  spotify_id TEXT,
  youtube_channel_id TEXT,
  image_url TEXT,
  language TEXT DEFAULT 'en',
  category TEXT[],
  episode_count INT DEFAULT 0,
  avg_accuracy_score DECIMAL(3,2),
  status TEXT DEFAULT 'active', -- active, paused, retired
  created_at TIMESTAMPTZ,
  updated_at TIMESTAMPTZ
);

-- Episodes
CREATE TABLE episodes (
  id UUID PRIMARY KEY,
  podcast_id UUID REFERENCES podcasts(id),
  title TEXT NOT NULL,
  slug TEXT NOT NULL,
  description TEXT,
  published_at TIMESTAMPTZ,
  duration_seconds INT,
  audio_url TEXT,
  transcript_source TEXT, -- youtube, spotify, whisper, manual
  transcript_status TEXT, -- pending, processing, complete, failed
  claim_count INT DEFAULT 0,
  processing_status TEXT DEFAULT 'queued',
  created_at TIMESTAMPTZ,
  UNIQUE(podcast_id, slug)
);

-- People (hosts & guests)
CREATE TABLE people (
  id UUID PRIMARY KEY,
  name TEXT NOT NULL,
  slug TEXT UNIQUE NOT NULL,
  bio TEXT,
  image_url TEXT,
  twitter_handle TEXT,
  linkedin_url TEXT,
  wikipedia_url TEXT,
  known_domains TEXT[], -- economics, science, tech, politics
  overall_accuracy DECIMAL(3,2),
  total_claims INT DEFAULT 0,
  total_scored_claims INT DEFAULT 0,
  total_appearances INT DEFAULT 0,
  created_at TIMESTAMPTZ,
  updated_at TIMESTAMPTZ
);

-- Person accuracy by domain
CREATE TABLE person_domain_scores (
  id UUID PRIMARY KEY,
  person_id UUID REFERENCES people(id),
  domain TEXT NOT NULL, -- economics, science, geopolitics, tech, health, etc.
  accuracy_score DECIMAL(3,2),
  claim_count INT,
  last_updated TIMESTAMPTZ,
  UNIQUE(person_id, domain)
);

-- Appearances (person ↔ episode)
CREATE TABLE appearances (
  id UUID PRIMARY KEY,
  person_id UUID REFERENCES people(id),
  episode_id UUID REFERENCES episodes(id),
  podcast_id UUID REFERENCES podcasts(id),
  role TEXT NOT NULL, -- host, guest, co-host
  UNIQUE(person_id, episode_id)
);

-- Transcripts (stored in chunks for search)
CREATE TABLE transcript_segments (
  id UUID PRIMARY KEY,
  episode_id UUID REFERENCES episodes(id),
  speaker_id UUID REFERENCES people(id),
  start_time DECIMAL(10,2),
  end_time DECIMAL(10,2),
  text TEXT NOT NULL,
  segment_index INT
);

-- Claims (the core data)
CREATE TABLE claims (
  id UUID PRIMARY KEY,
  episode_id UUID REFERENCES episodes(id),
  person_id UUID REFERENCES people(id),
  transcript_segment_id UUID REFERENCES transcript_segments(id),
  claim_text TEXT NOT NULL,
  original_quote TEXT, -- exact words from transcript
  timestamp_start DECIMAL(10,2),
  domain TEXT NOT NULL,
  claim_type TEXT NOT NULL, -- factual, prediction, causal, statistical, historical, scientific
  prediction_resolution_date DATE, -- for predictions
  status TEXT DEFAULT 'pending', -- pending, verified, false, mixed, unverifiable
  score INT, -- 1-5
  confidence TEXT, -- high, medium, low
  ai_extraction_confidence DECIMAL(3,2),
  created_at TIMESTAMPTZ,
  scored_at TIMESTAMPTZ,
  updated_at TIMESTAMPTZ
);

-- Verification evidence
CREATE TABLE verifications (
  id UUID PRIMARY KEY,
  claim_id UUID REFERENCES claims(id),
  source_url TEXT,
  source_name TEXT,
  source_type TEXT, -- government, academic, news, fact_check, database
  source_reliability TEXT, -- high, medium, low
  supports_claim BOOLEAN,
  excerpt TEXT,
  verified_by TEXT, -- ai, human, hybrid
  verifier_notes TEXT,
  created_at TIMESTAMPTZ
);

-- Disputes/Appeals
CREATE TABLE disputes (
  id UUID PRIMARY KEY,
  claim_id UUID REFERENCES claims(id),
  disputed_by TEXT, -- user_id or person_id
  reason TEXT NOT NULL,
  evidence_url TEXT,
  status TEXT DEFAULT 'open', -- open, under_review, resolved, rejected
  resolution TEXT,
  created_at TIMESTAMPTZ,
  resolved_at TIMESTAMPTZ
);

-- Indexes
CREATE INDEX idx_claims_person ON claims(person_id);
CREATE INDEX idx_claims_episode ON claims(episode_id);
CREATE INDEX idx_claims_domain ON claims(domain);
CREATE INDEX idx_claims_status ON claims(status);
CREATE INDEX idx_claims_score ON claims(score);
CREATE INDEX idx_appearances_person ON appearances(person_id);
CREATE INDEX idx_episodes_podcast ON episodes(podcast_id);
CREATE INDEX idx_transcript_episode ON transcript_segments(episode_id);
```

---

## 5. API Design (Key Endpoints)

```
# Search
GET  /api/search?q={query}&type=person|podcast|claim

# Podcasts
GET  /api/podcasts                          # List (paginated, filterable)
GET  /api/podcasts/:slug                    # Podcast profile
GET  /api/podcasts/:slug/episodes           # Episode list
GET  /api/podcasts/:slug/claims             # All claims from podcast

# People
GET  /api/people                            # List (paginated, filterable)
GET  /api/people/:slug                      # Person profile + scores
GET  /api/people/:slug/claims               # All claims by person
GET  /api/people/:slug/scores               # Domain breakdown + history
GET  /api/people/:slug/appearances          # Podcast appearances
GET  /api/people/:slug/predictions          # Predictions + resolutions

# Episodes
GET  /api/episodes/:id                      # Episode detail
GET  /api/episodes/:id/claims               # Claims from episode
GET  /api/episodes/:id/transcript           # Full transcript

# Claims
GET  /api/claims                            # List (heavily filterable)
GET  /api/claims/:id                        # Claim detail + evidence
POST /api/claims/:id/dispute                # Submit dispute

# Compare
GET  /api/compare?people=slug1,slug2        # Side-by-side comparison

# Trending
GET  /api/trending/claims                   # Trending/controversial claims
GET  /api/trending/people                   # Most discussed people

# Stats
GET  /api/stats/overview                    # Platform-wide stats
```

---

## 6. Tech Stack

### Recommended Stack

| Layer | Technology | Why |
|-------|-----------|-----|
| **Frontend** | Next.js 14+ (App Router) | SSR for SEO, React ecosystem, Vercel deployment |
| **Styling** | Tailwind CSS + shadcn/ui | Fast development, consistent design |
| **Backend** | Next.js API routes → migrate to separate service at scale | Start simple, separate when needed |
| **Database** | PostgreSQL (Supabase or Neon) | Robust, great for relational data, free tiers |
| **Search** | Typesense (MVP) → Elasticsearch (scale) | Typesense is free, fast, easy; ES for advanced features |
| **Cache** | Redis (Upstash) | Free tier, serverless, fast |
| **Object Storage** | S3 / R2 (Cloudflare) | R2 has no egress fees |
| **AI/LLM** | Claude API (extraction) + GPT-4o (verification) | Use best model per task |
| **Transcription** | Whisper large-v3 (self-hosted on GPU) or Deepgram API | Cost vs quality tradeoff |
| **Diarization** | pyannote.audio | Best open-source diarization |
| **Job Queue** | BullMQ + Redis | Reliable, battle-tested |
| **Hosting** | Vercel (frontend) + Railway/Fly.io (workers) | Cheap, scalable |
| **Monitoring** | Sentry + Axiom | Error tracking + logs |
| **Analytics** | PostHog (self-hosted) or Plausible | Privacy-friendly |
| **CDN** | Cloudflare | Free tier is excellent |

---

## 7. Phased Rollout

### Phase 1: MVP (Months 1-3) — Budget: $200-500/mo
**Goal:** Prove the concept works with 10-20 podcasts

- Manual podcast selection (high-profile shows with available transcripts)
- YouTube transcript scraping for initial data
- Basic claim extraction pipeline (Claude API)
- Simple verification against web search + known databases
- Static-ish site with person profiles and claim lists
- No user accounts yet
- Deploy on Vercel + Supabase free tiers
- **Success metric:** 1,000 claims scored, interesting findings to share on social

### Phase 2: Growth (Months 4-8) — Budget: $500-2,000/mo
**Goal:** Expand to top 200 podcasts, build audience

- Automated episode ingestion pipeline
- Whisper transcription for podcasts without existing transcripts
- Improved AI verification pipeline with RAG
- User accounts, following, notifications
- Search and filtering
- API v1 (free tier)
- SEO optimization, social sharing features
- **Success metric:** 50,000 claims, 10,000 monthly visitors

### Phase 3: Scale (Months 9-15) — Budget: $2,000-10,000/mo
**Goal:** Production grade, monetization begins

- Automated ingestion for top 2,000 podcasts
- Human review queue for high-stakes claims
- Premium features (API access, advanced analytics)
- B2B outreach to media companies
- Prediction tracking and resolution
- Community dispute/verification features
- **Success metric:** 500,000 claims, 100,000 monthly visitors, first revenue

### Phase 4: Platform (Months 16+) — Budget: scales with revenue
**Goal:** Become the standard for podcast credibility

- Open API with tiered pricing
- Browser extension (show scores while listening)
- Podcast platform integrations
- Community contributor program
- International expansion (non-English podcasts)
- Real-time processing (scores within hours of episode release)
- **Success metric:** 1M+ monthly visitors, self-sustaining revenue

---

## 8. Key Technical Risks & Mitigations

| Risk | Impact | Mitigation |
|------|--------|-----------|
| Claim extraction accuracy too low | Core product fails | Extensive prompt engineering, human review for MVP, continuous improvement |
| Fact verification unreliable | Trust destroyed | Multi-source verification, confidence scores, transparency |
| Speaker diarization errors | Wrong person credited | Cross-reference with episode metadata, manual correction queue |
| LLM costs too high at scale | Burns money | Self-hosted models for commodity tasks, API for complex ones |
| Podcast transcript quality varies | Garbage in, garbage out | Quality scoring per source, fallback to self-transcription |
| Legal threats from scored individuals | Existential | Strong legal framework, insurance, careful framing (see LEGAL.md) |

---

## 9. MVP Spec — What to Build First

### Week 1-2: Data Foundation
- Set up PostgreSQL schema on Supabase
- Build podcast/episode/person ingestion from PodcastIndex API
- Scrape YouTube transcripts for initial 10 podcasts
- Manual guest identification for initial episodes

### Week 3-4: AI Pipeline
- Claim extraction prompts (test extensively on sample transcripts)
- Basic classification (domain, type)
- Simple verification pipeline (web search + known fact databases)
- Scoring algorithm v1

### Week 5-6: Frontend
- Next.js app with core pages (home, podcast, person, claim)
- Search (Typesense)
- Clean, trustworthy design
- OG images for social sharing

### Week 7-8: Polish & Launch
- Process initial batch of episodes
- Quality review of scores
- Write "How it works" / methodology page
- Soft launch, share on social media

### Starting Podcasts (10-20, chosen for diversity + claim density):
1. **All-In Podcast** — Economics, tech, politics. High claim density. Perfect test case.
2. **Joe Rogan Experience** — Massive audience, diverse guests, lots of claims
3. **Lex Fridman Podcast** — Science, tech, deep conversations
4. **The Ben Shapiro Show** — Political claims, daily show = high volume
5. **Pod Save America** — Political claims from the other side (balance)
6. **Huberman Lab** — Health/science claims, hugely popular
7. **The All-In Podcast** — Already mentioned but deserves emphasis
8. **Hard Fork (NYT)** — Tech industry claims
9. **The Diary of a CEO** — Business/life claims
10. **Acquired** — Business history claims (verifiable!)
11. **My First Million** — Business/economic predictions
12. **The Prof G Pod** — Scott Galloway's economic predictions (prolific predictor)
13. **Breaking Points** — Political analysis from both sides
14. **Pivot** — Kara Swisher + Scott Galloway, tech/business predictions
15. **The Peter Attia Drive** — Health/longevity science claims

These cover: economics, politics (left+right), science, health, tech, business — and all have freely available transcripts via YouTube.

---

## 10. Architecture Decision Records

### ADR-001: Start with Next.js monolith, split later
**Decision:** Single Next.js app for frontend + API + background jobs
**Rationale:** Fastest path to MVP. Can extract services when needed.
**Revisit when:** API latency or job processing becomes a bottleneck.

### ADR-002: PostgreSQL as primary database
**Decision:** Use Postgres for everything initially
**Rationale:** Handles relational data well, JSONB for flexibility, great ecosystem.
**Revisit when:** Search needs outgrow pg_trgm, analytics need columnar storage.

### ADR-003: Use LLM APIs, not self-hosted models (for MVP)
**Decision:** Claude API for extraction, GPT-4o for verification
**Rationale:** Best quality, no infrastructure overhead. Cost acceptable at MVP scale.
**Revisit when:** Processing >1000 episodes/month, cost optimization needed.

### ADR-004: YouTube transcripts as primary source (MVP)
**Decision:** Scrape YouTube auto-captions as primary transcript source
**Rationale:** Free, covers most major podcasts, good enough quality.
**Revisit when:** Need podcasts not on YouTube, or quality issues.
