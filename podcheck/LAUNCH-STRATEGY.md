# PodCheck — Launch Strategy: Start with Science, Not Politics

## The Smart Sequencing

Jon's instinct is right: **start with black-and-white claims, not political ones.** Here's why this is strategically correct without limiting the final product.

---

## Phase 1 Topics: Provably Right or Wrong

### Tier 1 Launch Categories (Months 1-3)
These have **objective, citable sources**:

| Category | Why It's Clean | Example Claim | Verification Source |
|----------|---------------|---------------|-------------------|
| **Health/Science** | Peer-reviewed papers exist | "Cold exposure increases norepinephrine 200-300%" | PubMed, specific studies |
| **Statistics** | Government data is public | "US GDP grew 3% last quarter" | BEA, BLS, Fed |
| **Historical Facts** | Documented record | "The US has never defaulted on its debt" | Treasury records |
| **Tech/Engineering** | Measurable/documented | "GPT-4 has 1.8 trillion parameters" | Published papers, official specs |
| **Finance/Markets** | Numbers don't lie | "Bitcoin hit $100K in 2025" | Market data |

### Why These First
1. **Verdicts are defensible** — you can point to a paper, a dataset, a government report
2. **Low legal risk** — calling a health claim false with citations isn't defamation, it's science
3. **High demand** — health/science misinformation on podcasts is a HUGE pain point (Huberman, Rogan health claims, etc.)
4. **Bipartisan audience** — nobody's political identity is threatened by fact-checking a supplement claim
5. **Builds credibility** — when you eventually DO politics, you have a track record of accuracy

### Ideal Seed Podcasts for Phase 1
| Podcast | Primary Topics | Why Perfect |
|---------|---------------|-------------|
| **Huberman Lab** | Neuroscience, health | Tons of citable claims, massive audience, science-checkable |
| **Peter Attia's The Drive** | Medicine, longevity | Highly specific medical claims with paper references |
| **All-In Podcast** | Tech, economics, markets | Financial claims, market predictions, stats |
| **Lex Fridman** | AI, science, tech | Technical claims, research citations |
| **Acquired** | Business history, finance | Historical claims, financial data |
| **Freakonomics** | Economics, social science | Statistical claims, study references |
| **Found My Fitness** (Rhonda Patrick) | Nutrition, genetics | Extremely citation-dense, science claims |

These 7 podcasts give you hundreds of verifiable claims per month in clean, objective territory.

---

## What We're NOT Doing Yet (and Why)

### Phase 1 Exclusions
- ❌ Political opinion claims ("Biden's border policy is a disaster")
- ❌ Subjective value judgments ("That's the worst decision in history")
- ❌ Predictions about political outcomes ("Democrats will lose the Senate")
- ❌ Policy effectiveness debates ("Universal healthcare would save money")

### Why Exclude, Not Avoid Forever
Political claims ARE checkable — but they require more nuance:
- "The border saw 2.5M encounters last year" ← **checkable** (CBP data)
- "The border is a disaster" ← **opinion, skip**
- "This bill will cost $2T" ← **checkable** (CBO score) but contested

The issue isn't that political facts are unknowable — it's that **the audience will question your motives before they question the evidence.** If your first 100 verdicts include political ones, half the audience assumes you're biased before you've built any trust.

---

## The Credibility Ladder

```
Phase 1 (Months 1-3):  Science + Stats + Finance
                        → Build reputation as accurate and fair
                        → Nobody questions your motives

Phase 2 (Months 4-8):  Add Economics + Business + History
                        → Expand to slightly grayer areas
                        → Track record speaks for itself

Phase 3 (Months 9-12): Add Political FACTS (not opinions)
                        → "The unemployment rate is 4%" ← check it
                        → "The president's policy failed" ← skip it
                        → Methodology page is battle-tested

Phase 4 (Year 2+):     Full spectrum including political claims
                        → Brand is established
                        → Methodology is proven
                        → Audience trusts the process
```

**You're not limiting the product. You're building the trust equity needed to go everywhere.**

---

## The Transcript Question

### Skip Whisper. Start with Existing Transcripts.

Jon's right — avoid the audio processing complexity at MVP. Sources of existing transcripts:

| Source | Quality | Coverage | Cost |
|--------|---------|----------|------|
| **YouTube auto-captions** | Good (95%+) | Most major podcasts post to YT | Free (yt-dlp) |
| **Spotify transcripts** | Excellent | Growing coverage | Free (API/scrape) |
| **Podcast apps** (Apple, etc.) | Varies | Increasing | Free |
| **Manual (fan transcripts)** | Varies | Popular episodes | Free |
| **Deepgram/AssemblyAI** | Excellent | Any audio | $0.006/min (~$0.75/episode) |

### Recommended MVP Approach
1. **Primary**: YouTube auto-captions via yt-dlp (free, good quality, includes timestamps)
2. **Fallback**: Spotify transcripts where available
3. **Last resort**: Send audio to Deepgram API ($0.75/episode)

For the 7 seed podcasts, ALL of them post to YouTube. So Phase 1 transcription cost = **$0**.

### Speaker ID Without Diarization
For MVP, skip automated speaker diarization entirely:
- **YouTube captions sometimes include speaker labels**
- **Episode descriptions list guests**
- **LLM can infer speakers from context**: "As a neuroscientist, I think..." = probably the neuroscience guest
- **Manual tagging for the first 50 episodes** to build training data

This gets you 80% accuracy on speaker attribution with 0% of the diarization complexity.

---

## MVP Tech Stack (Simplified)

```
YouTube captions (yt-dlp)
    → Claude API (claim extraction + classification + verification)
        → Supabase (store claims, scores, profiles)
            → Next.js frontend (static generation for SEO)
```

That's it. Four components. No Whisper, no diarization, no Elasticsearch, no Redis. 

Add complexity only when you need it.

---

## What This Means for the Plan

### Does NOT limit the final product:
- ✅ Same architecture works for all topics
- ✅ Same scoring system applies to political claims
- ✅ Same UI/UX, just more categories
- ✅ B2B API is topic-agnostic from day one
- ✅ Brand reputation is STRONGER for starting neutral

### Does limit Phase 1 scope (intentionally):
- 🎯 7 podcasts instead of 15
- 🎯 4-5 claim categories instead of all
- 🎯 No audio processing pipeline
- 🎯 No political content moderation headaches
- 🎯 Lower legal risk surface

### Timeline impact:
Starting with science/stats claims actually **accelerates** MVP because:
- Verification is more automatable (check the paper, check the data)
- Fewer edge cases in claim classification
- Less human review needed
- No political bias concerns to design around

---

## The Narrative

Launch message: **"We're bringing scientific accuracy to the podcast world."**

Not: "We're fact-checking podcasters" (sounds adversarial)
Not: "We're fighting misinformation" (sounds political)

Instead: "We think podcast listeners deserve to know which claims are backed by evidence. We start with science and data because that's where truth is clearest."

That positioning is **unattackable.** Who's going to argue against checking if health claims are scientifically accurate?

Then you expand from a position of trust.
