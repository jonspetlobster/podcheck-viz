# PodCheck — Project Status Tracker

## 🟢 Complete
- [x] System architecture & plan (PLAN.md)
- [x] Business model (BUSINESS.md)
- [x] Legal analysis (LEGAL.md)
- [x] Data pipeline design (DATA.md)
- [x] UX wireframes (UX.md)
- [x] Competitive analysis (COMPETITIVE.md, COMPETITIVE-TEARDOWN.md)
- [x] Integration review (REVIEW.md)
- [x] Viral mechanics strategy (VIRAL-MECHANICS.md)
- [x] B2B API strategy (B2B-API.md)
- [x] Launch strategy — science first (LAUNCH-STRATEGY.md)
- [x] Full cost audit — $6/mo MVP (COST-AUDIT.md)
- [x] Domain research (DOMAINS.md)
- [x] Pipeline proof-of-concept (PIPELINE-TEST.md)
- [x] Prompt engineering — extraction + verification (podcheck-pipeline/prompts/)
- [x] MVP prototype with transcript context (podcheck-mvp/index.html)
- [x] 3 visual design variants (v-dark, v-light, v-bold)
- [x] Expanded dataset — 90 claims, 15 people, 7 shows (data-expanded.js)

## 🟡 In Progress
- [ ] Integrate expanded data + filters into MVP

## 🔴 Waiting on Jon
- [ ] **Name decision** — ClaimScore (.com+.io available), PodCheck (.io/.co/.app), or Verifax?
- [ ] **Visual direction** — Dark, Light, or Bold? (open all 3 in browser)
- [ ] **Domain purchase** — once name decided
- [ ] **Social accounts** — Twitter/X, GitHub org
- [ ] **LLC formation** — needed before any public launch
- [ ] **Budget confirmation** — $6/mo MVP, scaling to ~$2K/mo at Phase 2
- [ ] **Bootstrap vs seek funding?**
- [ ] **Solo vs co-founder?**
- [ ] **Review planning docs** — any feedback on the strategy?

## 🔧 Nova Needs (10x productivity unlocks — Jon to action)
1. [ ] **Host exec permission** — let Nova run commands on Mac directly (open browsers, serve localhost, install tools)
2. [ ] **Network access from sandbox** — so yt-dlp, pip, and curl work properly
3. [ ] **Headless Chrome / browser tool** — screenshots, registrar checks, verify how pages render
4. [ ] **Namecheap or Porkbun API key** — real domain availability + pricing
5. [ ] **Claude API key** — test extraction/verification prompts against real transcripts
6. [ ] **Supabase project** — set up real database for MVP
7. [ ] **Elevated permissions** — open files in browser, serve localhost to LAN
8. [ ] **GitHub token with delete scope** — current token can't delete repos (3 old ones still up)
9. [ ] **Rotate current GitHub token** — it's been in chat history, should be replaced
10. [ ] **Vercel account or deploy target** — to put MVP on the real web

## 📋 Ready to Build (awaiting green light)
- [ ] Wire up real pipeline: yt-dlp → Claude → Supabase
- [ ] Process first real episode end-to-end
- [ ] Set up GitHub org + repo
- [ ] Set up Supabase project
- [ ] Deploy MVP to chosen domain
