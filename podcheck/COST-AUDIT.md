# PodCheck — Full Pipeline Cost Audit

> Every tool, every step. What costs money, what's free, and where to cut.

---

## The Pipeline, Step by Step

### 1. PODCAST DISCOVERY
| Tool | Purpose | Cost | Free Alternative |
|------|---------|------|-----------------|
| PodcastIndex API | Find podcasts/episodes | **FREE** (open source) | — |
| Apple Podcasts API | Metadata/charts | **FREE** | — |
| Spotify API | Episode metadata | **FREE** | — |

**Cost: $0/mo** ✅

---

### 2. TRANSCRIPT ACQUISITION
| Tool | Purpose | Cost | Free Alternative |
|------|---------|------|-----------------|
| yt-dlp | YouTube caption download | **FREE** (open source) | — |
| Spotify transcript scrape | Pull Spotify transcripts | **FREE** | — |
| Whisper (self-hosted) | Audio → text | **FREE** (runs on GPU) | Only needed if no captions exist |
| Whisper API (OpenAI) | Audio → text (hosted) | **$0.006/min** (~$0.72/ep) | Self-host Whisper |
| Deepgram | Audio → text (hosted) | **$0.0043/min** (~$0.52/ep) | Self-host Whisper |
| AssemblyAI | Audio → text + diarization | **$0.006/min** (~$0.72/ep) | Self-host Whisper |

**MVP cost: $0/mo** — all 7 seed podcasts are on YouTube with captions
**Fallback cost: ~$0.50-0.75/episode** if audio-only

---

### 3. SPEAKER IDENTIFICATION
| Tool | Purpose | Cost | Free Alternative |
|------|---------|------|-----------------|
| pyannote.audio | Speaker diarization | **FREE** (open source, self-hosted) | — |
| NeMo | Speaker diarization | **FREE** (open source, NVIDIA) | — |
| AssemblyAI diarization | Cloud diarization | Included in transcription price | pyannote |
| Manual/LLM inference | Context-based speaker ID | Claude API cost (see below) | — |

**MVP cost: $0/mo** — skip diarization, use LLM context inference + episode metadata
**Scale cost: $0/mo** — pyannote is open source, runs on CPU

---

### 4. CLAIM EXTRACTION (LLM)
| Tool | Purpose | Cost | Free Alternative |
|------|---------|------|-----------------|
| **Claude API (Anthropic)** | Extract claims from transcript | **$3/M input, $15/M output** | — |
| GPT-4o (OpenAI) | Alternative LLM | **$2.50/M input, $10/M output** | — |
| GPT-4o-mini (OpenAI) | Cheaper extraction | **$0.15/M input, $0.60/M output** | — |
| Claude 3.5 Haiku | Cheaper extraction | **$0.25/M input, $1.25/M output** | — |
| Llama 3 (self-hosted) | Free extraction | **FREE** (needs GPU) | — |
| Gemini 2.0 Flash | Google's cheap option | **FREE tier** (1500 req/day) | — |

**Per-episode breakdown (2-hour podcast, ~25K words, ~35K tokens):**
| Model | Input Cost | Output Cost (~2K tokens) | Total/Episode |
|-------|-----------|-------------------------|---------------|
| Claude 3.5 Sonnet | $0.105 | $0.030 | **$0.135** |
| GPT-4o | $0.088 | $0.020 | **$0.108** |
| GPT-4o-mini | $0.005 | $0.001 | **$0.006** |
| Claude 3.5 Haiku | $0.009 | $0.003 | **$0.012** |
| Gemini Flash | Free tier | Free tier | **$0.00** |
| Llama 3 (self-hosted) | GPU cost | GPU cost | **~$0.002** |

**MVP recommendation: Haiku or GPT-4o-mini for extraction (~$0.01/episode)**
Use Sonnet/GPT-4o only for verification step where quality matters more.

---

### 5. CLAIM VERIFICATION (LLM + Search)
| Tool | Purpose | Cost | Free Alternative |
|------|---------|------|-----------------|
| **Claude Sonnet/GPT-4o** | Verify claims against sources | **$0.10-0.15/episode** | Use cheaper models |
| Google Search API | Find verification sources | **$5/1000 queries** | — |
| Serper.dev | Search API (cheaper) | **$50/mo for 5000 queries** | — |
| SerpAPI | Search API | **$50/mo for 5000 queries** | — |
| Brave Search API | Search API | **FREE** (2000 queries/mo) | — |
| Tavily | AI search API | **FREE tier** (1000/mo) | — |
| DuckDuckGo scrape | Free search | **FREE** (rate-limited) | — |
| Wikipedia API | Reference data | **FREE** | — |
| PubMed API | Medical/science papers | **FREE** | — |
| FRED API | Economic data (Fed) | **FREE** | — |
| BLS API | Labor/employment stats | **FREE** | — |
| Census API | Census data | **FREE** | — |
| SEC EDGAR | Financial filings | **FREE** | — |
| World Bank API | Global economic data | **FREE** | — |

**Per-episode verification (~15 claims average):**
| Component | Cost |
|-----------|------|
| LLM verification (Sonnet) | $0.10-0.15 |
| Web search (15 queries) | $0.075 (Serper) or FREE (Brave/Tavily) |
| Gov/academic APIs | $0.00 |
| **Total** | **$0.10-0.22/episode** |

**MVP recommendation:**
- Use Brave Search free tier (2000/mo) + gov APIs for verification sources
- That's ~130 episodes/mo of search for free
- Supplement with PubMed, FRED, Wikipedia (all free)
- **Verification cost: ~$0.10/episode** (just the LLM)

---

### 6. DATA STORAGE
| Tool | Purpose | Cost | Free Alternative |
|------|---------|------|-----------------|
| **Supabase** (Postgres) | Core database | **FREE** (500MB, 50K rows) | — |
| Supabase Pro | When you outgrow free | **$25/mo** (8GB, unlimited rows) | — |
| Neon (Postgres) | Alternative | **FREE** (512MB) | — |
| PlanetScale (MySQL) | Alternative | **$39/mo** | Supabase free |
| Elasticsearch | Full-text search | **$95/mo** (Elastic Cloud) | Supabase full-text search (FREE) |
| Redis | Caching | **$0-30/mo** (Upstash free tier) | In-memory cache in app |
| S3/R2 | Transcript storage | **~$0.02/GB** | Cloudflare R2 (FREE egress) |
| ClickHouse | Analytics | **FREE** (self-hosted) or $50/mo cloud | Skip for MVP |

**MVP cost: $0/mo** — Supabase free tier handles everything
**Scale cost: $25/mo** — Supabase Pro when you hit limits

---

### 7. HOSTING & FRONTEND
| Tool | Purpose | Cost | Free Alternative |
|------|---------|------|-----------------|
| **Vercel** | Next.js hosting | **FREE** (hobby) | — |
| Vercel Pro | More bandwidth/builds | **$20/mo** | — |
| Cloudflare Pages | Alternative hosting | **FREE** | — |
| Netlify | Alternative hosting | **FREE** (100GB bandwidth) | — |
| Domain name | podcheck.io or similar | **$10-40/year** | — |
| Cloudflare | DNS + CDN + DDoS | **FREE** | — |

**MVP cost: $0/mo** + ~$30/year domain
**Scale cost: $20/mo** (Vercel Pro)

---

### 8. MONITORING & OPS
| Tool | Purpose | Cost | Free Alternative |
|------|---------|------|-----------------|
| Sentry | Error tracking | **FREE** (5K events/mo) | — |
| Vercel Analytics | Web analytics | **FREE** (included) | — |
| Plausible | Privacy-focused analytics | **$9/mo** | Vercel Analytics (free) |
| UptimeRobot | Uptime monitoring | **FREE** (50 monitors) | — |
| GitHub | Code hosting | **FREE** | — |
| GitHub Actions | CI/CD | **FREE** (2000 min/mo) | — |

**MVP cost: $0/mo** ✅

---

### 9. AUTH & EMAIL
| Tool | Purpose | Cost | Free Alternative |
|------|---------|------|-----------------|
| Supabase Auth | User accounts | **FREE** (included) | — |
| Resend | Transactional email | **FREE** (100/day) | — |
| Loops / Buttondown | Newsletter | **FREE tier** | — |

**MVP cost: $0/mo** ✅

---

## TOTAL COST SUMMARY

### MVP (Phase 1) — 7 podcasts, ~30 episodes/month

| Category | Monthly Cost | Notes |
|----------|-------------|-------|
| Transcripts | **$0** | YouTube captions via yt-dlp |
| Claim Extraction (LLM) | **$0.36** | Haiku @ $0.012/ep × 30 |
| Claim Verification (LLM) | **$3.00** | Sonnet @ $0.10/ep × 30 |
| Web Search | **$0** | Brave Search free tier |
| Gov/Academic APIs | **$0** | All free |
| Database | **$0** | Supabase free |
| Hosting | **$0** | Vercel free |
| Domain | **$2.50** | ~$30/year |
| Monitoring | **$0** | Free tiers |
| **TOTAL** | **~$6/mo** | |

### Growth (Phase 2) — 100 podcasts, ~400 episodes/month

| Category | Monthly Cost | Notes |
|----------|-------------|-------|
| Transcripts | **$0-50** | Mostly YouTube; some Deepgram fallback |
| Claim Extraction | **$5** | Haiku @ $0.012/ep × 400 |
| Claim Verification | **$60** | Sonnet + search @ $0.15/ep × 400 |
| Web Search | **$50** | Serper.dev paid tier |
| Database | **$25** | Supabase Pro |
| Hosting | **$20** | Vercel Pro |
| Domain | **$2.50** | |
| Human fact-checker | **$2,000** | Part-time contractor |
| **TOTAL** | **~$2,200/mo** | (mostly the human) |

### Scale (Phase 3) — 500 podcasts, ~2000 episodes/month

| Category | Monthly Cost | Notes |
|----------|-------------|-------|
| Transcripts | **$200** | Mix of free + paid |
| LLM (all steps) | **$400** | Volume pricing |
| Search APIs | **$100** | |
| Database | **$100** | Supabase + extras |
| Hosting | **$50** | |
| Human fact-checkers (3) | **$8,000** | |
| **TOTAL** | **~$9,000/mo** | (mostly humans) |

---

## The Big Insight

**The pipeline is basically free until you add humans.**

| Component | % of Phase 1 Cost | % of Phase 3 Cost |
|-----------|-------------------|-------------------|
| AI/LLM | 55% | 4% |
| Infrastructure | 40% | 4% |
| Humans | 0% | **89%** |
| Search APIs | 0% | 1% |
| Transcription | 0% | 2% |

The tech scales cheaply. The humans don't. This means:
1. **Phase 1 is nearly free** — you can run this for coffee money
2. **Human review is the real cost** — delay hiring until you must
3. **Invest in making the AI pipeline accurate enough to minimize human review**

---

## Tools to SKIP for MVP

Previously in the plan but unnecessary:
| Planned Tool | Why Skip | When to Add |
|-------------|----------|-------------|
| Whisper | YouTube captions are free | When covering audio-only podcasts |
| pyannote/NeMo | Context inference works for known podcasts | When scaling to unknown formats |
| Elasticsearch | Supabase full-text search works | When search volume demands it |
| Redis | App-level caching sufficient | When you have concurrent users |
| ClickHouse | No analytics needed yet | When B2B clients want dashboards |
| GraphQL | REST is fine | When multiple clients need different shapes |
| WebSockets | No real-time needed | When doing live fact-checking |

**Cut 7 tools. Save months of development. Same product.**
