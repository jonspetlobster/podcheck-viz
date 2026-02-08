# PodCheck — Integration Review & Executive Summary

*All 6 planning documents reviewed and cross-referenced.*

---

## Executive Summary

PodCheck is a website that grades podcast guests and hosts on the accuracy of their claims. AI extracts claims from freely available transcripts, fact-checks them against authoritative sources, and builds public accuracy profiles — like a credit score for credibility.

**The opportunity is real.** No one is doing this. The closest comparisons (NewsGuard rates websites, PolitiFact rates political claims manually) validate the category but leave podcast-level, person-level accuracy scoring wide open. The technology to do this at scale only matured in 2024-2025 with LLM capabilities and ubiquitous transcription.

**The plan is comprehensive.** Six documents totaling ~230KB cover technical architecture, business model, legal analysis, data pipeline, UX design, and competitive landscape.

**Below: key findings, conflicts to resolve, decisions needed, and a prioritized action plan.**

---

## ✅ Where All Plans Align

These elements are consistent across all docs:

1. **Start with 10-20 high-profile podcasts** — All-In, Rogan, Huberman, Lex, Shapiro, Pod Save America, Attia, etc. Diverse topics + politics to avoid bias perception.
2. **Freemium model** — Free profiles + paid premium ($9.99/mo) + B2B API ($99-999/mo). No advertising. Trust is the product.
3. **Next.js + Postgres + Claude/GPT** — Monolith to start, split later. Supabase free tier for MVP.
4. **MVP budget: $200-500/mo** — Achievable out of pocket. No fundraising needed for Phase 1.
5. **YouTube transcripts first** — Free, covers ~70% of target podcasts. Whisper fallback for the rest.
6. **Scoring scale: letter grade (A-F) + percentage** — Like Rotten Tomatoes. Instantly understood.
7. **Prediction tracking is a killer feature** — Nobody does this. It's uniquely viral and defensible.
8. **Non-partisanship is existential** — Score across the political spectrum from Day 1. Transparent methodology. Diverse advisory board.

---

## ⚠️ Conflicts & Gaps Between Documents

### Conflict 1: Scoring Scale
- **PLAN.md** proposes a 1-5 numeric scale per claim (Verified/Mostly True/Mixed/Mostly False/False)
- **UX.md** proposes letter grades A-F with percentages for aggregate scores
- **BUSINESS.md** references PolitiFact's Truth-O-Meter and suggests "personality" in the scale
- **Resolution needed:** The per-claim scale and per-person aggregate scale should be distinct. **Recommendation:** Keep 5-tier verdict per claim (True → False) + letter grade aggregate per person. Add personality to the claim verdicts (not the aggregate). Consider branded tier names for virality.

### Conflict 2: What Gets Scored
- **PLAN.md** includes predictions as "Pending" with future resolution
- **DATA.md** includes predictions in the claim taxonomy
- **LEGAL.md** says to EXCLUDE predictions from scoring (they're not verifiable yet)
- **UX.md** has prediction tracking as a feature
- **Resolution:** Predictions should be TRACKED but NOT included in the aggregate accuracy score until resolved. They're a separate "Forecasting Score" feature. This resolves the legal concern while preserving the killer feature.

### Conflict 3: Private Figures
- **LEGAL.md** is emphatic: NEVER score private individuals (Tier 3 — one-time guests, non-public figures)
- **DATA.md** and **PLAN.md** don't mention this distinction
- **Resolution:** Build a "public figure determination process" into the pipeline. Automated check: Does this person have a Wikipedia page? >10K social followers? Multiple podcast appearances? Regular media presence? If none, don't score. **This must be in the MVP.**

### Conflict 4: Humor/Satire Detection
- **LEGAL.md** flags this as critical for defamation defense
- **DATA.md** mentions excluding jokes but doesn't detail how
- **PLAN.md** says "jokes/sarcasm (detected via context)"
- **Gap:** No detailed plan for sarcasm/humor detection. This is hard and important. **Recommendation:** For MVP, add a LLM pass that flags potential humor/sarcasm. Conservative approach: when in doubt, exclude. Build a "comedy podcast" exclusion list.

### Conflict 5: Geographic Scope
- **LEGAL.md** recommends geo-blocking UK and Australia profiles initially
- **BUSINESS.md** doesn't mention geographic restrictions
- **UX.md** doesn't account for geo-restricted content
- **Resolution:** MVP is US-focused. Don't proactively scrape UK/AU-only podcasts. For global podcasts (Rogan, Huberman), serve everyone but be prepared to geo-block specific profiles if legal threats come from those jurisdictions.

### Gap 1: No Name Decision
- **BUSINESS.md** recommends "PodCheck" with podcheck.ai domain
- But no final decision is documented
- **Action needed:** Jon needs to check domain availability and decide.

### Gap 2: No Detailed Human Review Workflow
- All docs mention "human review" but none specify the actual workflow tool, queue management, or reviewer training.
- **Recommendation for MVP:** Simple Google Sheet or Notion board. Claims flagged for review go into a queue. Reviewer checks evidence, approves/modifies/rejects AI verdict. Graduate to a proper tool in Phase 2.

### Gap 3: No Content Calendar for Launch
- **BUSINESS.md** describes marketing strategy but no specific launch sequence
- **Recommendation:** Build a 4-week launch calendar as a separate doc

---

## 🔴 Critical Decisions Jon Must Make

| # | Decision | Options | My Recommendation | Urgency |
|---|----------|---------|-------------------|---------|
| 1 | **Product name** | PodCheck, ClaimScore, ClaimCheck, other | PodCheck — clear, memorable, verb-able | This week |
| 2 | **Domain** | podcheck.ai, podcheck.com, other | Buy podcheck.ai now ($10-50) | This week |
| 3 | **Entity formation** | LLC vs C-Corp, CA vs DE | California LLC for now. Convert to DE C-Corp if/when raising | Before any public launch |
| 4 | **Budget commitment** | $200-500/mo | $300/mo covers everything for Phase 1 | Now |
| 5 | **Bootstrap vs grants** | Pure bootstrap, apply for grants, pre-seed | Bootstrap Phase 1 + apply to Knight Foundation at Month 6 | Decide now, act later |
| 6 | **Solo vs co-founder** | Jon alone + Nova, or recruit a technical co-founder | Start alone + Nova. Recruit if traction proves out. | Not yet |
| 7 | **Social accounts** | Which platforms, when to create | Create X/Twitter + Reddit now. Claim handles before launch. | This week |
| 8 | **Part-time fact-checker** | Hire at MVP or wait? | Hire from Month 2. Journalism grad student, 10hr/week, $20/hr | Month 2 |

---

## 📋 Prioritized Action Plan

### Week 1 (Immediate)
- [ ] Jon: Buy domain (podcheck.ai or podcheck.com)
- [ ] Jon: Create X/Twitter account (@PodCheck or @PodCheckAI)
- [ ] Jon: Create Reddit account (age it before launch)
- [ ] Nova: Set up Next.js project with Supabase
- [ ] Nova: Build database schema (from PLAN.md)
- [ ] Nova: Build YouTube transcript scraper (yt-dlp integration)

### Week 2
- [ ] Jon: File LLC (Stripe Atlas or CA Secretary of State)
- [ ] Nova: Build claim extraction pipeline (Claude API)
- [ ] Nova: Process first 5 episodes of All-In Podcast as proof of concept
- [ ] Nova: Build basic person + claim data model

### Week 3-4
- [ ] Nova: Build basic frontend (homepage, person profile, claim detail)
- [ ] Nova: Process 10-15 podcasts, first 2-3 episodes each
- [ ] Jon: Review initial claim extractions and verdicts for quality
- [ ] Nova: Iterate on extraction/verification prompts based on quality review

### Week 5-6
- [ ] Nova: Add search (Typesense)
- [ ] Nova: Build OG image generation for social sharing
- [ ] Nova: Polish UX, implement design system
- [ ] Jon: 1-hour consultation with media law attorney

### Week 7-8
- [ ] Nova: Process full back-catalog of seed podcasts (recent 3-6 months)
- [ ] Jon: Recruit part-time fact-checker
- [ ] Human review of all published verdicts
- [ ] Nova: Write methodology page
- [ ] Soft launch to friends/family for feedback

### Month 3
- [ ] Public launch
- [ ] First social media posts (X/Twitter threads with interesting findings)
- [ ] First newsletter issue
- [ ] Monitor for legal/PR issues

---

## 💰 Phase 1 Budget Summary

| Item | Monthly | Notes |
|------|---------|-------|
| Domain | $1/mo amortized | One-time ~$12/year |
| Hosting (Vercel free) | $0 | Free tier covers MVP |
| Database (Supabase free) | $0 | Free tier: 500MB, 50K rows |
| Claude API | $50-150 | ~100 episodes × $0.80 |
| Fact-checker (Month 2+) | $800 | 10hr/week × $20/hr |
| LLC filing | $8/mo amortized | One-time ~$100 |
| **Total Month 1** | **$50-150** | |
| **Total Month 2+** | **$860-950** | |
| **6-Month Total** | **~$5,000** | |

---

## 🎯 Success Metrics (Phase 1)

| Metric | Target | How to Measure |
|--------|--------|---------------|
| Claims processed | 1,000+ | Database count |
| People with profiles | 200+ | Database count |
| Podcasts covered | 15-20 | Database count |
| Claim extraction precision | >85% | Human spot-check |
| Verdict accuracy | >80% | Agreement with human reviewers |
| Social shares | 100+ | OG image loads + share clicks |
| First viral moment | 1 | A tweet/post that gets >10K impressions |

---

## 🏗️ Architecture Summary (Simplified)

```
PHASE 1 STACK (keep it simple):

YouTube → yt-dlp → Transcript
                      ↓
              Claude API → Claims extracted
                      ↓
              Claude API + Data APIs → Claims verified
                      ↓
              Supabase (Postgres) → Stored
                      ↓
              Next.js → Displayed
                      ↓
              Vercel → Served
                      ↓
              Cloudflare → Cached
```

No Kubernetes. No microservices. No Elasticsearch. No Redis. Just a Next.js app, a database, and Claude. Add complexity only when the simple thing breaks.

---

## 🔑 The Single Most Important Insight

Across all 6 documents, one theme emerges:

**The data IS the product, the marketing, AND the moat.**

Every accuracy score is a piece of content. Every surprising finding is a social media post. Every scored claim is an SEO page. Every prediction tracked is a future viral moment. Every profile is a page someone will Google.

PodCheck doesn't need a marketing budget. It needs accurate, interesting data about famous people — and the data produces its own distribution.

**Build the pipeline. Score the claims. The rest follows.**

---

*Ready for your review, Jon. What questions do you have? What decisions can we make right now?*
