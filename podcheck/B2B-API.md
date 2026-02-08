# PodCheck — B2B API: The Real Revenue Engine

## Why B2B Is the Money

Consumer freemium gets you users and brand. B2B API gets you revenue. Here's who pays and why:

---

## Tier 1: Podcast Booking Platforms ($$$)

### The Customers
- **Matchmaker.fm** — podcast guest matching
- **Podchaser** — podcast database/discovery
- **Rephonic** — podcast PR and analytics
- **SquadCast, Riverside** — recording platforms
- **Podcast networks** (Wondery, Gimlet, Spotify Studios) — internal tools

### What They Buy
```
GET /api/v1/person/{slug}/score
→ { grade: "B+", accuracy: 0.76, claims_checked: 847, last_updated: "2026-02-07" }

GET /api/v1/person/{slug}/summary
→ { grade, accuracy, top_topics, strongest_area, weakest_area, trend, claim_count }
```

### Why They Pay
- **Booking platforms**: Add "Accuracy Score" to guest profiles. Differentiator over competitors.
- **Recording platforms**: Show guest scores before/after recording. "Your guest has a C+ in health claims — you may want to verify."
- **Podcast networks**: Internal quality control. Vet guests before they go on flagship shows.

### Pricing
| Tier | Calls/mo | Price | Target |
|------|----------|-------|--------|
| Starter | 1,000 | $200/mo | Small platforms |
| Growth | 10,000 | $500/mo | Mid-size tools |
| Enterprise | 100,000+ | $2,000-5,000/mo | Major platforms |
| Custom | Unlimited | Negotiated | Spotify, Apple |

---

## Tier 2: Media & Journalism ($$$)

### The Customers
- **News organizations**: NYT, WaPo, Reuters, AP
- **Fact-checking orgs**: Snopes, PolitiFact (yes, they'd use this)
- **Media monitoring**: Meltwater, Cision, Brandwatch
- **News aggregators**: Apple News, Google News, Flipboard

### What They Buy
```
GET /api/v1/person/{slug}/claims?topic=economics&verdict=false
→ [{ claim, verdict, confidence, sources, episode, date }]

GET /api/v1/trending/claims?period=7d
→ [{ claim, person, verdict, virality_score, dispute_count }]

GET /api/v1/search/claims?q="inflation rate"&verdict=false
→ [{ claim, person, context, verdict, evidence }]
```

### Why They Pay
- **Pre-interview prep**: Journalist booking a podcast guest can pull their full claim history. "You said X on Rogan last month — our data shows that's false."
- **Story leads**: "Which claims went viral this week and were wrong?" = instant article ideas
- **Source verification**: Before citing a podcast clip in a news story, check the speaker's accuracy
- **Real-time election coverage**: Cross-reference candidate claims against PodCheck database

### Pricing
| Tier | Price | Features |
|------|-------|----------|
| Newsroom Basic | $300/mo | 5,000 lookups, claim search, person profiles |
| Newsroom Pro | $1,000/mo | Unlimited, bulk export, custom alerts, webhook |
| Enterprise | $3,000+/mo | Raw data access, custom integrations, SLA |

---

## Tier 3: PR & Communications ($$$$$)

### The Customers
- **PR agencies**: Edelman, Weber Shandwick, FleishmanHillard
- **Talent agencies**: CAA, WME, UTA (podcast divisions)
- **Corporate comms teams**: Tech companies, pharma, finance
- **Reputation management firms**: Any firm managing public figures

### What They Buy
```
GET /api/v1/person/{slug}/report
→ Full accuracy report: grade, trends, topic breakdown, worst claims, 
  correction opportunities, comparison to peers

POST /api/v1/alerts
→ { person_slug, threshold: "below_B", notify: "webhook" }
  (Alert when client's score drops)

GET /api/v1/person/{slug}/recommendations
→ { corrections_needed: [...], topics_to_avoid: [...], 
    talking_points_with_sources: [...] }
```

### Why They Pay
This is the **highest-value segment**. PR firms will pay premium because:

- **Pre-appearance prep**: Before a client goes on a podcast, pull their accuracy report. Brief them on which past claims to correct, which topics to be careful on.
- **Reputation monitoring**: Real-time alerts when a client's score changes. "Your client just dropped to C+ after the Rogan appearance."
- **Competitive intel**: "Your client has a B+. Their competitor has a C-. Here's the messaging angle."
- **Damage control**: When a client gets a FALSE verdict on a viral claim, PR team needs to respond fast. PodCheck alert → correction strategy → on-air correction → score recovery.
- **Booking optimization**: "Which shows have the highest average guest accuracy? Book your client there for credibility by association."

### Pricing
| Tier | Price | Features |
|------|-------|----------|
| Agency Basic | $500/mo | 5 monitored profiles, weekly reports |
| Agency Pro | $2,000/mo | 25 profiles, real-time alerts, competitor tracking |
| Enterprise | $5,000+/mo | Unlimited, dedicated support, custom analytics |

---

## Tier 4: Ad Tech & Sponsors ($$$$$ — Phase 3+)

### The Customers
- **Podcast ad networks**: Megaphone, Acast, Podscribe
- **Brand safety platforms**: DoubleVerify, IAS, Oracle/Moat
- **Direct advertisers**: Consumer brands spending on podcast ads

### What They Buy
```
GET /api/v1/podcast/{slug}/brand-safety
→ { accuracy_grade, controversial_claims_30d, topic_risk_scores, 
    advertiser_suitability: "high" }

GET /api/v1/episode/{id}/brand-safety
→ { claims_flagged: 2, topics: ["politics", "health"], 
    risk_level: "medium", details: [...] }
```

### Why They Pay
- **Brand safety**: Advertisers already pay for brand safety in video/display. Podcasts are a blind spot. PodCheck fills it.
- **Ad placement optimization**: "Don't run our pharma ad next to a show where the host regularly makes false health claims"
- **Premium CPM justification**: Shows with high accuracy scores can charge more for ads. "Our host has an A- on PodCheck — your ad runs alongside verified credibility."

This is where the **BIG money** is long-term. Brand safety in podcasting is a largely unsolved problem. PodCheck becomes the standard.

### Pricing
| Tier | Price | Features |
|------|-------|----------|
| Platform | $3,000-10,000/mo | Bulk podcast/episode scoring, API integration |
| Enterprise | $10,000+/mo | Real-time scoring, custom models, SLA |

---

## API Design Philosophy

### Make It Stupidly Easy to Integrate
```bash
# One line to get a score
curl -H "Authorization: Bearer pk_live_xxx" \
  https://api.podcheck.io/v1/person/andrew-huberman/score

# Response
{
  "person": "Andrew Huberman",
  "slug": "andrew-huberman",
  "grade": "B",
  "accuracy": 0.76,
  "claims_checked": 847,
  "trend": "stable",
  "last_updated": "2026-02-07T12:00:00Z",
  "profile_url": "https://podcheck.io/person/andrew-huberman",
  "badge_url": "https://podcheck.io/badge/andrew-huberman.svg"
}
```

### SDK Support (Phase 2)
```python
# Python
from podcheck import PodCheck
pc = PodCheck("pk_live_xxx")
score = pc.person("andrew-huberman").score()
print(f"{score.name}: {score.grade} ({score.accuracy}%)")
```

```javascript
// JavaScript
import { PodCheck } from '@podcheck/sdk';
const pc = new PodCheck('pk_live_xxx');
const score = await pc.person('andrew-huberman').score();
```

### Webhook Events
```json
{
  "event": "score.changed",
  "person": "andrew-huberman",
  "old_grade": "B+",
  "new_grade": "B",
  "trigger_claim": "claim_48291",
  "timestamp": "2026-02-07T15:30:00Z"
}
```

Partners get real-time updates. No polling needed.

---

## Revenue Projections

### Year 1 (Conservative)
| Segment | Customers | Avg MRR | Total MRR |
|---------|-----------|---------|-----------|
| Booking Platforms | 3 | $400 | $1,200 |
| Media/Journalism | 5 | $500 | $2,500 |
| PR/Comms | 2 | $1,000 | $2,000 |
| **Total** | **10** | | **$5,700/mo** |

### Year 2 (Growth)
| Segment | Customers | Avg MRR | Total MRR |
|---------|-----------|---------|-----------|
| Booking Platforms | 10 | $600 | $6,000 |
| Media/Journalism | 20 | $700 | $14,000 |
| PR/Comms | 10 | $2,000 | $20,000 |
| Ad Tech | 3 | $5,000 | $15,000 |
| **Total** | **43** | | **$55,000/mo** |

### Year 3 (Scale)
If you're the standard: $100K-200K MRR from API alone. Consumer premium adds another $30-50K.

---

## The Lock-In Effect

Once a platform integrates PodCheck's API:
- Their users see PodCheck scores
- Their users expect PodCheck scores
- Removing the integration = feature regression
- Switching to a competitor = different scores = confusion

**API integrations are incredibly sticky.** Churn rates for well-integrated B2B APIs are typically <5%/year.

---

## Go-to-Market for B2B

### Phase 1: Free Tier to Build Adoption
- **Free API**: 100 calls/day, basic score endpoint only
- Target: indie developers, small podcast tools
- Purpose: build ecosystem, get integrations, generate case studies

### Phase 2: Convert Power Users
- When free users hit limits, offer paid tiers
- Direct outreach to booking platforms and PR firms
- "You're already using us — here's what premium gets you"

### Phase 3: Enterprise Sales
- Dedicated sales (first sales hire)
- Custom integrations, SLAs, on-prem options
- Annual contracts, volume discounts

### The Free-to-Paid Pipeline
```
Free API (100/day) → Developer builds integration → Users love it → 
Developer hits limits → Pays for Growth tier → Company scales → 
Enterprise deal → Annual contract → Lock-in
```

---

## The Strategic Position

PodCheck's B2B API becomes **infrastructure for trust in media**. Like how:
- Stripe is infrastructure for payments
- Twilio is infrastructure for communications  
- PodCheck becomes infrastructure for credibility

You're not selling a product. You're selling **the truth layer** that every media platform needs.
