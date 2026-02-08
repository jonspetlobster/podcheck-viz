# PodCheck — Legal Analysis

**Product:** A website that publicly grades podcast guests and hosts on claim accuracy. AI extracts claims from transcripts, fact-checks them, and builds public accuracy profiles for named individuals.

**Prepared:** February 2026
**Status:** Working legal analysis — not legal advice. Engage counsel before launch.

---

## Table of Contents

1. [Defamation & Libel Risk](#1-defamation--libel-risk)
2. [Fair Use & Copyright](#2-fair-use--copyright)
3. [Section 230](#3-section-230)
4. [Privacy & Data Protection](#4-privacy--data-protection)
5. [Dispute & Appeals Process](#5-dispute--appeals-process)
6. [Legal Infrastructure](#6-legal-infrastructure)
7. [EU AI Act](#7-eu-ai-act)
8. [Content Moderation Framework](#8-content-moderation-framework)
9. [Risk Matrix](#9-risk-matrix)

---

## 1. Defamation & Libel Risk

This is the **single largest legal exposure** for PodCheck. The product's core function — publishing accuracy scores tied to named individuals — is inherently reputational. Every score is a statement about a person's credibility.

### 1.1 The Core Exposure

Defamation requires: (1) a false statement of fact, (2) published to third parties, (3) about an identifiable person, (4) causing reputational harm. PodCheck hits elements 2, 3, and 4 by design. The entire legal strategy must focus on ensuring element 1 — falsity — never applies, or that the statements are protected opinion/fair comment.

**Key distinction:** There is a critical legal difference between:

- **"Joe Rogan's claim verification rate is 72%"** — This is a factual assertion about a methodology's output. If the methodology is sound and transparently disclosed, this is defensible as a true statement about the system's findings.
- **"Joe Rogan is 72% truthful"** — This is an assertion about a person's character. It implies knowledge of their mental state and intent. This is far more legally exposed.

**The framing matters enormously.** PodCheck must never characterize individuals as liars, dishonest, or untruthful. Every score must be framed as an output of a disclosed methodology applied to specific, verifiable claims.

### 1.2 Public Figure Doctrine — *New York Times Co. v. Sullivan*, 376 U.S. 254 (1964)

The *Sullivan* standard is PodCheck's strongest shield in the United States. Under this doctrine:

- **Public figures** (and public officials) must prove **actual malice** — that the defendant made the statement **knowing it was false** or with **reckless disregard** for its truth or falsity.
- **Private figures** need only prove **negligence** under *Gertz v. Robert Welch, Inc.*, 418 U.S. 323 (1974).

**Application to PodCheck:**

Most podcast hosts and frequent guests are likely **limited-purpose public figures** — they have voluntarily injected themselves into public discourse on specific topics. Under *Gertz*, a limited-purpose public figure is someone who "voluntarily injects himself or is drawn into a particular public controversy and thereby becomes a public figure for a limited range of issues." Going on a public podcast to make public claims on public issues is a textbook example.

However, **not every podcast guest is a public figure.** A private individual who appears once on a friend's small podcast to discuss a personal experience may remain a private figure. PodCheck should implement tiering:

- **Tier 1 — Clear public figures:** Politicians, media personalities, authors, public intellectuals, executives of public companies. Full profiles, strong *Sullivan* protection.
- **Tier 2 — Limited-purpose public figures:** Recurring podcast guests, subject-matter experts with public platforms, activists. Profiles limited to their area of public commentary.
- **Tier 3 — Private individuals:** One-time guests, personal storytellers, non-public persons. **Do not score.** The negligence standard is too easy to meet, and the reputational harm claim is stronger.

**Key cases:**

- *Milkovich v. Lorain Journal Co.*, 497 U.S. 1 (1990): Even statements framed as "opinion" can be actionable if they imply undisclosed defamatory facts. A score that implies "this person lies" without disclosing the methodology could be treated as an implied factual assertion. **Mitigation:** Always disclose methodology, always link to the underlying claim and source.
- *Hustler Magazine v. Falwell*, 485 U.S. 46 (1988): Public figures cannot recover for intentional infliction of emotional distress based on speech unless they meet the *Sullivan* standard. Helpful for PodCheck if a public figure claims emotional harm from a low score.
- *Curtis Publishing Co. v. Butts*, 388 U.S. 130 (1967): Extended *Sullivan* to public figures, not just public officials.

### 1.3 How Analogous Platforms Survive

**Credit Reporting Agencies (Equifax, Experian, TransUnion):**
- Operate under the **Fair Credit Reporting Act (FCRA)**, 15 U.S.C. § 1681 et seq.
- Key protections: statutory framework mandates accuracy procedures, dispute resolution, and limits on who can access reports.
- **Lesson for PodCheck:** Having a robust, documented accuracy methodology and dispute process is legally protective. Credit agencies get sued constantly but survive because they follow statutory procedures. PodCheck has no equivalent statute but should voluntarily adopt analogous rigor.

**Yelp:**
- Protected primarily by **Section 230** of the CDA (user-generated reviews).
- *Kimzey v. Yelp!, Inc.*, 836 F.3d 1263 (9th Cir. 2016): Yelp's star ratings derived from user reviews are protected by Section 230.
- **Distinction:** PodCheck generates its own scores via AI — it's not aggregating user opinions. This weakens the Section 230 argument (see Section 3 below).

**Glassdoor:**
- Also relies on Section 230 for user-generated content.
- *Glassdoor, Inc. v. Superior Court*, 9 Cal. App. 5th 623 (2017): Glassdoor can protect anonymous reviewer identities under First Amendment.

**RateMyProfessors:**
- User-generated ratings = Section 230 protection.
- Ratings are understood as subjective opinions, not factual assertions.
- **Key distinction from PodCheck:** RateMyProfessors publishes opinions. PodCheck publishes what it frames as factual analysis. Factual assertions get less First Amendment protection than opinions.

**Consumer Reports / Wirecutter:**
- Closest analogy. These publish expert evaluations and ratings.
- *Bose Corp. v. Consumers Union*, 466 U.S. 485 (1984): The Supreme Court upheld First Amendment protection for product reviews, applying *Sullivan*-like scrutiny. The court found Consumer Reports' review was protected even though it contained factual errors, because there was no actual malice.
- **This is PodCheck's best analogy.** Frame the product as editorial analysis / consumer reporting about public claims.

### 1.4 Anti-SLAPP Protections

Anti-SLAPP (Strategic Lawsuit Against Public Participation) statutes allow defendants to quickly dismiss meritless defamation suits and recover attorney's fees. These are **critical** for PodCheck.

**State-by-state landscape:**

| Jurisdiction | Statute | Strength | Notes |
|---|---|---|---|
| **California** | Cal. Civ. Proc. Code § 425.16 | ★★★★★ | Broadest protection. Covers statements on public issues. Mandatory fee-shifting. PodCheck should incorporate and HQ here. |
| **Texas** | Tex. Civ. Prac. & Rem. Code § 27.001 | ★★★★☆ | Strong. Amended in 2019 to narrow slightly but still robust. |
| **New York** | N.Y. Civ. Rights Law §§ 70-a, 76-a | ★★★☆☆ | Strengthened in 2020. Applies to public speech on matters of public interest. |
| **Oregon** | ORS § 31.150 | ★★★★☆ | Broad, covers statements made in public forums. |
| **Florida** | Fla. Stat. § 768.295 | ★★☆☆☆ | Narrow. Limited to government proceedings. Largely useless for PodCheck. |
| **Washington** | RCW § 4.24.525 | ★★★★☆ | Strong protections, fee-shifting. |
| **Federal** | None | N/A | No federal anti-SLAPP statute as of 2026. The SPEAK FREE Act has been introduced repeatedly but not passed. |

**Strategic implication:** PodCheck should incorporate in California or Delaware (for corporate law advantages) and designate California as its principal place of business. Forum selection clauses in ToS should specify California courts.

### 1.5 Safe Framing

**DO use:**
- "Claim verification rate" — implies a methodological process, not a character judgment
- "X of Y verifiable claims were supported by [source type]"
- "Based on our methodology, this claim could not be verified"
- "Verification pending" for claims not yet checked
- "Disputed by [source]" with linked citations

**DO NOT use:**
- "Truthfulness score" — implies knowledge of intent
- "Honesty rating" — character judgment
- "Liar" / "dishonest" / "misleading" — these are defamatory per se in most jurisdictions
- Any language implying the person *knowingly* made false statements
- Aggregate scores without the ability to drill into individual claims

**The "verifiable claims" filter is critical.** PodCheck should only score claims that are objectively verifiable (factual assertions with determinable truth values). Opinions, predictions, value judgments, and subjective assessments must be excluded from scoring. This both improves accuracy and reduces legal exposure.

### 1.6 Per-Jurisdiction Analysis

#### United States
- **Standard:** *Sullivan* actual malice for public figures; negligence for private figures (*Gertz*).
- **Key advantage:** Strong First Amendment protection for speech on matters of public concern.
- **Key risk:** State-by-state variation in defamation law. Some states (e.g., New Hampshire) have plaintiff-friendly precedent.
- **Damages:** Compensatory, and potentially punitive if actual malice is shown.
- **Recommendation:** PodCheck is most defensible under US law. Incorporate here.

#### United Kingdom
- **Standard:** Defamation Act 2013 requires "serious harm" to reputation (s.1). For bodies trading for profit, "serious financial loss" (s.1(2)).
- **Key risk:** UK is historically plaintiff-friendly. **No actual malice requirement.** Truth (s.2), honest opinion (s.3), and public interest (s.4) are statutory defenses.
- **Defense strategy:**
  - **Truth defense (s.2):** If PodCheck's scores accurately reflect the methodology's output, and the methodology is disclosed, the factual assertion ("our system rated this claim as unverified") is true.
  - **Honest opinion (s.3):** The score could be characterized as an opinion based on disclosed facts (the underlying claims and sources).
  - **Public interest (s.4):** Publication of accuracy information about public commentary is in the public interest if the publisher reasonably believed so. See *Economou v. de Freitas* [2018] EWCA Civ 2591.
- **Serious harm threshold:** *Lachaux v. Independent Print Ltd* [2019] UKSC 27 — claimant must prove serious harm as a fact, not just tendency. This helps PodCheck for subjects with large platforms who would struggle to show actual reputational damage from a score.
- **Recommendation:** Serve UK users but **geo-block profile pages for individuals who send valid UK legal complaints** until local counsel reviews. Consider not proactively indexing UK-only podcasts initially.

#### European Union
- **Standard:** Varies by member state. Generally, defamation is both a civil and criminal matter in many EU jurisdictions (e.g., Germany's § 186-187 StGB, France's Loi du 29 juillet 1881).
- **Key risk:** Criminal defamation exists in most EU states. Germany's right of personality (*Persönlichkeitsrecht*) under Art. 1-2 GG provides broad protection.
- **Germany:** *BVerfG, 1 BvR 1783/05* (Stolpe decision) — statements must be clearly distinguishable as fact or opinion. Ambiguous statements are interpreted in the way most favorable to the speaker, but only if the speaker has made the factual basis clear.
- **France:** Defamation under the 1881 Press Law. Truth is a defense (*exceptio veritatis*) but only for facts less than 10 years old and not relating to private life.
- **Recommendation:** EU is high-risk. Consider geo-blocking profiles or requiring opt-in for EU-based podcast guests. GDPR adds additional exposure (see Section 4).

#### Australia
- **Standard:** Uniform defamation law across states (Model Defamation Provisions, updated 2021). "Serious harm" threshold added in 2021 amendments.
- **Key case:** *Google LLC v. Defteros* [2022] HCA 27 — Google was held liable as a publisher of search results linking to defamatory content. This could extend to AI-generated summaries.
- **Defenses:** Truth (justification), honest opinion, fair comment on matter of public interest.
- **Key risk:** Australia lacks anti-SLAPP protections. Defamation suits are common and relatively easy to bring.
- **Recommendation:** High risk jurisdiction. Consider excluding Australian podcast guests from scoring or requiring them to opt in.

---

## 2. Fair Use & Copyright

### 2.1 Who Owns Podcast Transcripts?

Copyright in a podcast episode is complex and multilayered:

- **The sound recording:** Owned by the producer/publisher (typically the podcast network or host under work-for-hire or assignment).
- **The spoken words as literary work:** This is contested. In *Estate of Martin Luther King, Jr., Inc. v. CBS, Inc.*, 194 F.3d 1211 (11th Cir. 1999), the court held that King's "I Have a Dream" speech was not published merely by being delivered publicly. Spoken words in a podcast, once fixed in a recording, are copyrightable.
- **The transcript as a derivative work:** A transcript is a fixation of the spoken words. If created by the copyright holder, it's part of the original work. If created by a third party (including AI), the transcription process itself likely lacks sufficient originality to create a new copyright (*Feist Publications v. Rural Telephone*, 499 U.S. 340 (1991)), but the underlying words remain copyrighted.

**Practical answer:** The podcast producer/host likely holds copyright in both the recording and the underlying spoken content. Guests may have a claim to their own words, but absent a written agreement, this is murky. Joint authorship under 17 U.S.C. § 101 requires intent to create a joint work, which casual podcast appearances likely don't satisfy.

### 2.2 Transformative Use Argument

PodCheck's strongest copyright defense is **fair use** under 17 U.S.C. § 107. The four-factor test:

**Factor 1 — Purpose and character of the use:**
- PodCheck is **highly transformative**. It does not republish podcasts or provide a listening experience. It extracts discrete factual claims, evaluates them against external sources, and generates an analytical score. This is analogous to a book review, academic critique, or journalistic fact-check.
- *Google LLC v. Oracle America, Inc.*, 593 U.S. 1 (2021): The Supreme Court emphasized that transformative use creates "something new" with a different purpose. PodCheck's purpose (accuracy verification) is fundamentally different from the podcast's purpose (entertainment/information).
- *Andy Warhol Foundation v. Goldsmith*, 598 U.S. 508 (2023): Narrowed transformative use — the new use must have a "fundamentally different and new" purpose, not just add new aesthetics. PodCheck should survive this because fact-checking serves a different purpose than podcasting. However, *Warhol* signals courts may be less generous with transformative use claims.
- Commercial nature cuts slightly against fair use but is not dispositive.

**Factor 2 — Nature of the copyrighted work:**
- Podcasts are creative works, which cuts slightly against fair use.
- However, PodCheck targets **factual claims** within podcasts, not creative expression. Facts are not copyrightable (*Feist*).

**Factor 3 — Amount and substantiality:**
- PodCheck should quote only the specific claim being verified, not extended passages.
- **Safe harbor:** Quote the individual sentence or short passage containing the claim, with timestamp and episode attribution. Avoid quoting extended dialogue, narrative, or creative elements.
- *Harper & Row v. Nation Enterprises*, 471 U.S. 539 (1985): Even small quotations can be infringing if they constitute the "heart of the work." PodCheck should ensure it's not selectively quoting the most newsworthy/valuable portions of an episode in a way that substitutes for listening.

**Factor 4 — Market impact:**
- PodCheck does not substitute for the podcast listening experience. It arguably **drives traffic** to podcasts by creating engagement and discussion.
- No reasonable consumer would use PodCheck claim-by-claim breakdowns as a substitute for listening to a podcast.
- This factor strongly favors fair use.

**Overall fair use assessment:** Strong, but not bulletproof. The key is disciplined implementation — quote minimally, always link to the source, never provide enough content to substitute for the original.

### 2.3 Quoting Guidelines

| Context | Safe Amount | Risky |
|---|---|---|
| Individual claim verification | 1-3 sentences containing the claim | Extended dialogue around the claim |
| Episode summary | Brief factual description of topics covered | Detailed plot/narrative summary |
| Profile page | Aggregated scores with linked claims | Compiled transcript excerpts |
| Search results | Claim snippet + score | Full claim context |

**Best practice:** Display the exact quote containing the claim (typically one sentence), the timestamp, and a link to the original episode. Offer no more context than necessary for the user to understand what claim is being evaluated.

### 2.4 YouTube Auto-Captions vs. Official Transcripts

- **YouTube auto-generated captions:** YouTube's ToS (Section 6.C) grants YouTube a license but does not transfer copyright. Auto-captions are machine-generated transcriptions of copyrighted audio. Using them as a source doesn't change the copyright analysis — the underlying spoken words are still copyrighted by the creator.
- **Official transcripts:** Same copyright analysis. Using a creator-published transcript vs. generating your own doesn't change fair use — but using an official transcript might expose you to breach of the platform's ToS if you scraped it.
- **PodCheck should generate its own transcripts** using speech-to-text AI (Whisper, Deepgram, etc.) from the publicly available audio. This avoids any contractual issues with YouTube/Spotify ToS while the copyright fair use analysis remains the same.

### 2.5 DMCA Considerations

PodCheck should implement a **DMCA takedown procedure** (17 U.S.C. § 512) even though it's primarily using content under fair use:

- **Register a DMCA agent** with the Copyright Office and publish contact information on the site.
- **Implement notice-and-takedown:** If a copyright holder sends a valid DMCA notice regarding quoted material, remove the specific quote promptly. The score/analysis can remain if it's rewritten to describe the claim without quoting.
- **Counter-notification process:** If PodCheck believes the use is fair, it can file a counter-notification, and the material is restored in 10-14 business days unless the claimant files suit.
- **Repeat infringer policy:** Required for safe harbor eligibility.

**Note:** DMCA safe harbor primarily protects service providers hosting user content. Since PodCheck generates its own content, safe harbor may not apply. But having the process demonstrates good faith and provides a practical mechanism for resolving disputes without litigation.

---

## 3. Section 230

### 3.1 Does Section 230 Apply to AI-Generated Scores?

**Section 230(c)(1)** of the Communications Decency Act provides: "No provider or user of an interactive computer service shall be treated as the publisher or speaker of any information provided by another information content provider."

**The critical question:** Is PodCheck an "information content provider" for its AI-generated scores?

**The answer is likely yes — PodCheck is the content provider, and Section 230 does not protect its own content.**

- 47 U.S.C. § 230(f)(3) defines "information content provider" as "any person or entity that is responsible, in whole or in part, for the creation or development of information."
- PodCheck designs the AI, trains/configures the models, defines the scoring methodology, and publishes the output. It is the creator of the scores.
- *Fair Housing Council v. Roommates.com*, 521 F.3d 1157 (9th Cir. 2008): A website that requires users to provide information that itself becomes the basis for discrimination is an information content provider for that content. By analogy, PodCheck "develops" the scores through its AI system.
- *Force v. Facebook, Inc.*, 934 F.3d 53 (2d Cir. 2019): Algorithmic amplification of user content doesn't make a platform a content provider. But PodCheck isn't amplifying user content — it's creating original analytical content.

**However, there's a partial argument for Section 230 protection:**

- The underlying claims being analyzed are third-party content (the podcast guests' statements).
- PodCheck could argue it's providing "neutral tools" that process third-party content, similar to how search engines index and rank third-party websites.
- *Carafano v. Metrosplash.com, Inc.*, 339 F.3d 1119 (9th Cir. 2003): Providing neutral tools for organizing user content maintains Section 230 protection.

**Recent trends (2024-2026):**
- Courts are increasingly scrutinizing AI-generated content. The trend is toward treating AI outputs as the responsibility of the deployer, not as third-party content.
- *Gonzalez v. Google LLC*, 598 U.S. 617 (2023): The Supreme Court punted on whether algorithmic recommendations fall outside Section 230, leaving uncertainty.
- *Lemmon v. Snap Inc.*, 995 F.3d 1085 (9th Cir. 2021): Platform features that go beyond neutral content organization may not be protected.

### 3.2 Structuring for Maximum Protection

Even if Section 230 doesn't fully protect AI-generated scores, PodCheck can structure to maximize available protection:

1. **Hybrid model:** Allow users to submit corrections, additional context, and counter-evidence. User-contributed content is clearly protected by Section 230. If the scoring system incorporates user inputs, there's a stronger argument that the platform is facilitating third-party expression.

2. **Separation of content types:**
   - The underlying podcast quotes → third-party content (Section 230 applies)
   - The AI-generated fact-check and sources → PodCheck's own editorial content (Section 230 likely does not apply)
   - User corrections and comments → third-party content (Section 230 applies)

3. **Editorial disclaimer:** Frame scores as "automated analysis" rather than editorial assertions. This doesn't change the legal analysis but affects perception.

4. **Don't editorialize beyond the algorithm:** If PodCheck employees manually adjust scores or add editorial commentary, the entire output is more clearly PodCheck's own speech.

### 3.3 Bottom Line on Section 230

**Do not rely on Section 230 as a primary defense.** Build the product assuming PodCheck is the publisher of its scores and will be held responsible for their accuracy. Section 230 may provide ancillary protection for user-generated portions of the site but should not be the legal strategy's foundation.

---

## 4. Privacy & Data Protection

### 4.1 GDPR (EU/EEA/UK)

PodCheck's core function — building profiles of named individuals based on their public statements — triggers multiple GDPR provisions.

#### 4.1.1 Lawful Basis for Processing

PodCheck processes personal data (names, statements, images, professional affiliations, accuracy scores). It needs a lawful basis under Article 6(1):

- **Consent (Art. 6(1)(a)):** Impractical. PodCheck can't obtain consent from every podcast guest before scoring them.
- **Legitimate interest (Art. 6(1)(f)):** Most viable. PodCheck can argue:
  - **Legitimate interest:** Public interest in accuracy of public discourse; consumers' interest in knowing the reliability of information sources.
  - **Necessity:** Automated analysis is the only practical way to assess claim accuracy at scale.
  - **Balancing test:** The data subjects are public figures making public statements on public platforms. Their reasonable expectation of privacy in these statements is low. The processing (accuracy analysis) is directly related to the public-facing activity.
  - **Safeguards:** Dispute process, right to correction, transparency about methodology.
- **Journalism exemption (Art. 85):** Member states must reconcile GDPR with freedom of expression. Many provide exemptions for journalistic purposes. PodCheck's fact-checking function is arguably journalistic. In the UK, the Data Protection Act 2018, Schedule 2, Part 5 provides a journalism exemption. Germany's *Medienprivileg* (media privilege) in state press laws provides similar protection.

#### 4.1.2 Automated Decision-Making and Profiling (Art. 22)

Article 22(1) provides: "The data subject shall have the right not to be subject to a decision based solely on automated processing, including profiling, which produces legal effects concerning him or her or similarly significantly affects him or her."

**Does PodCheck's scoring "significantly affect" data subjects?**

- An accuracy score published online can affect a person's professional reputation, speaking opportunities, and public perception.
- The Article 29 Working Party (now EDPB) Guidelines on Automated Decision-Making (WP251rev.01) state that "significantly affects" includes effects on reputation.
- **Risk:** A data subject could argue that PodCheck's score constitutes automated profiling that significantly affects them, triggering the right to human intervention, the right to contest the decision, and the right to an explanation.

**Mitigation:**
- Implement meaningful human review for disputed scores.
- Provide clear explanation of the methodology.
- Offer an accessible dispute/appeal process.
- Consider whether Art. 22(2) exceptions apply — particularly Art. 22(2)(a) (necessary for contract) or Art. 22(2)(c) (explicit consent). Neither is ideal. The **journalism/public interest** route under Art. 85 is stronger.

#### 4.1.3 Right to Erasure / Right to Be Forgotten (Art. 17)

Data subjects can request deletion of their personal data. However, Art. 17(3) provides exceptions:

- **(a) Freedom of expression and information:** If PodCheck's processing is for journalistic or public interest purposes, it can refuse erasure requests.
- **(d) Public interest in public health / archiving / scientific research:** Potentially applicable to maintaining accuracy records for public discourse.

**Practical approach:**
- Accept erasure requests.
- Evaluate under Art. 17(3)(a) — does the public interest in maintaining the accuracy record outweigh the individual's right to erasure?
- For public figures making public claims: likely yes, refuse erasure, document reasoning.
- For private individuals or old/irrelevant claims: likely comply with erasure.
- Always document the balancing exercise.

#### 4.1.4 Right to Rectification (Art. 16)

Data subjects have the right to correct inaccurate personal data. This aligns well with PodCheck's interests — if a score is wrong, PodCheck *wants* to correct it. Build rectification into the dispute process.

#### 4.1.5 Data Protection Impact Assessment (Art. 35)

PodCheck's processing — systematic, large-scale profiling of individuals based on public statements — almost certainly requires a DPIA. Conduct one before launching in the EU. Document:
- Nature, scope, context, purpose of processing
- Necessity and proportionality
- Risks to data subjects
- Mitigation measures

### 4.2 CCPA / CPRA (California)

- PodCheck collects "personal information" (names, public statements, derived scores) about California residents.
- **Right to know:** Consumers can request what personal information is collected. PodCheck must disclose.
- **Right to delete:** Similar to GDPR erasure. Provide mechanism for deletion requests.
- **Right to opt-out of sale:** PodCheck should not "sell" personal information. If it does (e.g., API access to scores), it must honor opt-out requests and display "Do Not Sell My Personal Information" link.
- **Profiling under CPRA:** The CPRA added rights related to automated decision-making technology. The California Privacy Protection Agency (CPPA) is developing regulations on this. Monitor developments.
- **Publicly available information exception:** CCPA § 1798.140(v)(2) excludes "publicly available" information from the definition of personal information. Podcast statements are publicly available. However, the *derived scores* are not publicly available information — they are PodCheck's creation. The exception likely covers the inputs but not the outputs.

### 4.3 Building Profiles Without Consent

- **US:** Generally permissible for publicly available information. No federal privacy statute prohibits aggregating and analyzing public statements. State laws vary but none currently prohibit this activity.
- **EU/UK:** Requires lawful basis (see GDPR analysis above). Consent is one basis but not the only one. Legitimate interest is the practical path.
- **Key principle:** PodCheck processes *public statements made in public forums*. The reasonable expectation of privacy is minimal. Courts have consistently held that individuals who make public statements have diminished privacy interests in those statements. *Von Hannover v. Germany (No. 2)* [2012] ECHR 228 — even for public figures, privacy must be balanced, but public statements on public matters receive less protection.

---

## 5. Dispute & Appeals Process

A robust dispute process is not just good practice — it's a **legal necessity** that strengthens defenses across defamation, GDPR, and general liability.

### 5.1 Dispute System Design

**Tier 1 — Automated Dispute (Response: 24-48 hours)**
1. Subject or any user clicks "Dispute" on a specific claim score.
2. Provides counter-evidence (links, documents, context).
3. AI re-evaluates the claim with the new evidence.
4. If the score changes, update immediately and note the correction.
5. If the score doesn't change, provide explanation and escalation option.

**Tier 2 — Human Review (Response: 5-10 business days)**
1. Escalated disputes reviewed by a trained human fact-checker.
2. Reviewer examines original claim, PodCheck's analysis, and the counter-evidence.
3. Decision: affirm, modify, or retract the score.
4. Written explanation provided to the disputant.

**Tier 3 — Independent Review (Response: 30 days)**
1. For persistent disputes, offer review by an independent fact-checking organization (e.g., IFCN-certified member).
2. Their decision is binding on PodCheck.
3. Cost borne by PodCheck (establishes good faith).

**All tiers:**
- Log every dispute, decision, and outcome.
- Publish aggregate dispute statistics (transparency report).
- Never retaliate by adjusting other scores for the same individual.

### 5.2 Handling Cease & Desist Letters

**Upon receipt of a C&D:**

1. **Do not panic. Do not immediately comply.** A C&D is a letter, not a court order.
2. **Log and preserve:** Timestamp receipt, preserve all related content and internal communications (litigation hold).
3. **Notify counsel** within 24 hours.
4. **Evaluate the claim:**
   - Is the underlying score accurate and defensible?
   - Is the subject a public or private figure?
   - What jurisdiction is the threat from?
   - Is the claim specific or a blanket "take everything down" demand?
5. **Response options:**
   - **If the score is defensible:** Respond politely, explain the methodology, offer the dispute process, decline to remove.
   - **If there's a legitimate error:** Correct the error, respond acknowledging the correction, thank them for bringing it to your attention.
   - **If from a high-risk jurisdiction (UK, Australia, EU):** Consider geo-blocking the specific profile for that jurisdiction while evaluating.
6. **Template response** (have counsel prepare) that:
   - Acknowledges receipt
   - Explains PodCheck's methodology and editorial process
   - Invites specific factual objections through the dispute process
   - Notes applicable legal protections (First Amendment, anti-SLAPP)
   - Does not make admissions

### 5.3 Correction Policy

- **Corrections are prominently displayed** — not buried. When a score changes due to a correction, the history is visible.
- **Correction log** on each profile page showing what changed and why.
- **No silent edits.** Every change to a score is logged with a timestamp and reason.
- **Proactive corrections:** If PodCheck discovers an error in its methodology that affected scores, proactively notify affected individuals and publish a correction notice.

### 5.4 Transparency Reports

Publish quarterly transparency reports including:
- Number of claims analyzed
- Number of disputes received
- Dispute outcomes (affirmed / modified / retracted)
- Number of legal threats received (anonymized)
- Methodology changes
- Error rates and accuracy metrics for the AI system itself

---

## 6. Legal Infrastructure

### 6.1 Terms of Service

The ToS must include:

- **Disclaimer:** "PodCheck provides automated analysis of publicly available statements. Scores reflect our methodology's output and should not be interpreted as assertions about any individual's character, honesty, or intent."
- **Limitation of liability:** Cap liability at fees paid (for premium users) or exclude consequential damages entirely.
- **Arbitration clause:** Mandatory arbitration with class action waiver. *Epic Systems Corp. v. Lewis*, 584 U.S. 497 (2018) upheld class action waivers in arbitration agreements. This prevents costly class actions by scored individuals.
- **Forum selection:** California courts (for non-arbitrable claims).
- **Choice of law:** California law.
- **DMCA notice procedures.**
- **Dispute process description.**
- **Acceptable use policy** prohibiting users from using scores to harass individuals.
- **Data licensing terms** if offering API access to scores.

### 6.2 Entity Structure

**Recommended: Delaware C-Corp with California principal place of business.**

- **Why Delaware:** Best-developed corporate law, Court of Chancery expertise, favorable liability protections. If seeking venture capital, investors expect Delaware incorporation.
- **Why California HQ:** Anti-SLAPP protection (strongest in the nation), favorable case law on speech rights, proximity to tech talent and legal expertise.
- **Alternative (bootstrap):** California LLC. Simpler, cheaper, pass-through taxation. Convert to C-Corp if/when raising institutional capital.
- **Consider separate entities** for different jurisdictions if expanding internationally. A US parent with a UK subsidiary (for GDPR compliance) may be appropriate.

### 6.3 Insurance

**Essential policies:**

1. **Media liability / Publisher's liability insurance:** Covers defamation, libel, slander, invasion of privacy, copyright infringement. This is the **single most important policy** for PodCheck. Expect $2,000-10,000/year for $1-5M coverage depending on scale.

2. **Errors & Omissions (E&O) / Professional liability:** Covers claims arising from errors in PodCheck's professional service (inaccurate scores, methodology failures).

3. **Cyber liability insurance:** Covers data breaches, which are relevant given the personal data PodCheck processes.

4. **General commercial liability:** Standard business coverage.

**Get insurance before launching.** Some policies have "prior acts" exclusions — claims arising from activities before the policy inception date are excluded.

### 6.4 When to Get a Lawyer

**Before launch:**
- Review ToS, Privacy Policy, and dispute process
- Conduct DPIA for GDPR compliance
- Review scoring methodology for defamation exposure
- Set up DMCA agent registration
- Establish C&D response procedures

**Ongoing:**
- Any C&D letter or legal threat (within 24 hours)
- Before expanding to UK, EU, or Australia
- Any change to scoring methodology that could affect legal exposure
- Before publishing scores for any individual who has previously objected
- Before any PR or media appearance discussing the product
- Annually for ToS/privacy policy review

**What kind of lawyer:**
- **First Amendment / media law attorney** — primary outside counsel
- **IP attorney** — for copyright fair use guidance
- **Privacy attorney** — for GDPR/CCPA compliance
- These can often be found at the same firm. Look for firms with media company clients.

---

## 7. EU AI Act

### 7.1 Classification Under the EU AI Act

The EU AI Act (Regulation (EU) 2024/1689) classifies AI systems by risk level. PodCheck's AI system:

- **Not "unacceptable risk"** (Art. 5) — It doesn't involve social scoring by governments, real-time biometric surveillance, or manipulation of vulnerable groups.
- **Possibly "high-risk"** — Article 6 and Annex III list high-risk categories. The closest is Annex III(4): "Employment, workers management and access to self-employment" — if PodCheck scores affect hiring/professional decisions. Also potentially Annex III(5)(a): AI systems evaluating creditworthiness, by analogy to "reputational scoring."
- **More likely "limited risk"** — Requiring transparency obligations but not the full compliance burden of high-risk systems.

**However,** the Act's general-purpose AI provisions (Art. 51-56) may apply if PodCheck uses a general-purpose AI model (e.g., GPT-4, Claude) for fact-checking. The provider of the GPAI model has separate obligations, but PodCheck as the deployer must ensure:

- Transparency that content is AI-generated
- Sufficient technical documentation
- Compliance with applicable copyright law (training data)

### 7.2 Transparency Requirements

Regardless of risk classification, the EU AI Act requires:

1. **Art. 50(1):** Persons interacting with an AI system must be informed they are interacting with AI (unless obvious from context).
2. **Art. 50(2):** AI-generated content must be labeled as such.
3. **Art. 50(4):** Deployers of AI systems that generate "deep fakes" or manipulated content must disclose this. Not directly applicable but signals legislative intent toward transparency.

**PodCheck must:**
- Clearly label all scores as "AI-generated analysis"
- Disclose the AI models and methodology used
- Provide a mechanism for individuals to contest AI-generated assessments
- Maintain records of AI system operation and outputs

### 7.3 Effective Dates

- Most provisions: August 2, 2026 (imminent at time of writing)
- Prohibited AI practices: Already in effect (February 2, 2025)
- High-risk AI obligations: August 2, 2027

**PodCheck should assume it needs to comply with EU AI Act transparency requirements by August 2026 if serving EU users.**

---

## 8. Content Moderation Framework

### 8.1 What Gets Scored vs. Excluded

**Score — Verifiable factual claims:**
- Statistical assertions ("Unemployment is at 3.5%")
- Historical claims ("The US entered WWII in 1941")
- Scientific claims ("Ivermectin cures COVID" / "Vaccines cause autism")
- Attributions ("The WHO said X")
- Quantifiable claims ("This company has 10,000 employees")

**Exclude — Non-verifiable statements:**
- Opinions ("I think the economy is doing poorly")
- Predictions ("Bitcoin will hit $100K by 2025")
- Value judgments ("That policy is terrible")
- Personal experiences ("When I had COVID, I felt...")
- Hypotheticals ("If we did X, then Y would happen")
- Rhetorical questions
- Jokes and sarcasm (see below)

### 8.2 Handling Satire, Humor, and Hypotheticals

**This is critical for defamation defense.** Scoring a comedian's joke as "false" would be both legally dangerous and editorially absurd.

**Detection and exclusion criteria:**
1. **Context flags:** If the podcast is categorized as comedy, satire, or entertainment, apply heightened screening.
2. **Speaker flags:** Known comedians, satirists, and entertainers should have different thresholds.
3. **Linguistic markers:** Obvious hyperbole ("the best thing in the history of the universe"), irony markers, setup-punchline structures.
4. **"Reasonable listener" test:** Would a reasonable listener understand this as a sincere factual assertion? If not, exclude.
5. **When in doubt, exclude.** The cost of excluding a genuine factual claim is low (slightly incomplete profile). The cost of scoring a joke as false is high (defamation exposure, reputational harm to PodCheck).

### 8.3 Political Neutrality

PodCheck will be **immediately attacked as politically biased** regardless of how it's built. This is inevitable and must be planned for.

**Structural protections:**
1. **Methodology transparency:** Publish the complete methodology. When people can see how scores are computed, bias claims are harder to sustain.
2. **Source diversity:** Use multiple fact-checking sources across the political spectrum. Don't rely solely on sources perceived as left-leaning (Snopes, PolitiFact) or right-leaning.
3. **Proportional coverage:** Track and publish the political distribution of scored individuals. If 80% of scored podcasters are from one political orientation, address this proactively.
4. **Subject-matter exclusions:** Consider excluding inherently contested political value claims. "Tax cuts grow the economy" vs. "Tax cuts shrink revenue" — these are debated empirical claims where reasonable economists disagree. Scoring them binary invites bias accusations.
5. **Confidence levels:** Instead of binary true/false, use a spectrum: Verified / Mostly Supported / Mixed Evidence / Mostly Unsupported / Unverified. This reduces perceived bias by acknowledging nuance.
6. **Advisory board:** Establish a politically diverse advisory board to review methodology and flag potential bias issues. Publicize the board's composition.

---

## 9. Risk Matrix

### 9.1 Risk Assessment

| Risk | Likelihood | Impact | Combined Risk | Primary Mitigation |
|---|---|---|---|---|
| **Defamation suit — US public figure** | Medium | High | 🟠 HIGH | *Sullivan* defense, anti-SLAPP, methodology transparency, insurance |
| **Defamation suit — US private figure** | Low | Very High | 🟠 HIGH | Don't score private figures. Period. |
| **Defamation suit — UK** | Medium | Very High | 🔴 CRITICAL | Geo-blocking, local counsel, careful launch strategy |
| **Defamation suit — Australia** | Medium | High | 🔴 CRITICAL | Geo-blocking or opt-in only |
| **Copyright infringement claim** | Medium | Medium | 🟡 MODERATE | Minimal quoting, fair use compliance, DMCA process |
| **GDPR complaint / enforcement** | High | High | 🔴 CRITICAL | DPIA, legitimate interest documentation, dispute process, DPO |
| **CCPA/CPRA compliance failure** | Medium | Medium | 🟡 MODERATE | Privacy policy, deletion mechanism, opt-out for data sale |
| **EU AI Act non-compliance** | Medium | Medium | 🟡 MODERATE | Transparency labeling, documentation, monitoring regulations |
| **Political bias accusations** | Very High | Medium | 🟠 HIGH | Methodology transparency, source diversity, advisory board |
| **AI hallucination / false score** | High | Very High | 🔴 CRITICAL | Human review layer, confidence thresholds, rapid correction process |
| **SLAPP suit in non-SLAPP state** | Low | High | 🟡 MODERATE | Forum selection clause, California incorporation |
| **Class action by scored individuals** | Low | Very High | 🟠 HIGH | Arbitration clause with class waiver in ToS |
| **Regulatory investigation (FTC)** | Low | High | 🟡 MODERATE | Accuracy in marketing claims, transparent methodology |
| **Platform ToS violation (scraping)** | Medium | Low | 🟢 LOW | Generate own transcripts from public audio feeds |

### 9.2 Specific Mitigations Summary

1. **Defamation:** Frame as methodology output, not character assessment. Strong anti-SLAPP jurisdiction. Media liability insurance. Never score private figures.
2. **Copyright:** Minimal quoting, transformative use, DMCA compliance. Generate own transcripts.
3. **Privacy:** GDPR-compliant legitimate interest basis. DPIA. Dispute process. Right to rectification built in.
4. **AI errors:** Confidence scoring, human review for disputed claims, transparent correction process, no scores published below a confidence threshold.
5. **Political bias:** Transparent methodology, diverse sources, advisory board, proportional coverage monitoring.

### 9.3 Red Lines — Never Cross These

1. **Never call anyone a "liar," "dishonest," or "untruthful."** Only describe claim verification rates.
2. **Never score private individuals.** Build and enforce a public figure determination process.
3. **Never score claims from private conversations, leaked audio, or non-public sources.** Only publicly published podcast episodes.
4. **Never publish a score without the ability to drill into the underlying claims and sources.** Every score must be fully auditable by the subject.
5. **Never ignore a dispute.** Every dispute gets a response, even if the answer is "we reviewed and stand by our analysis."
6. **Never manually adjust a score for editorial or commercial reasons.** Methodology must be consistently applied.
7. **Never sell individual accuracy data to employers, insurers, or financial institutions.** This transforms the product from journalism into a credit-reporting-like service with entirely different regulatory exposure (and possible FCRA applicability).
8. **Never retain data from jurisdictions where you've received valid legal orders to remove it,** unless you have counsel's advice to contest.
9. **Never launch in the UK, EU, or Australia without local legal counsel review.**
10. **Never publish scores for minors.** Implement age screening in the public figure determination process.

---

## Appendix A: Key Cases Reference

| Case | Citation | Relevance |
|---|---|---|
| *NYT v. Sullivan* | 376 U.S. 254 (1964) | Actual malice standard for public figures |
| *Gertz v. Welch* | 418 U.S. 323 (1974) | Private figure negligence standard |
| *Milkovich v. Lorain Journal* | 497 U.S. 1 (1990) | Implied factual assertions in opinions |
| *Bose v. Consumers Union* | 466 U.S. 485 (1984) | First Amendment protection for product reviews |
| *Google v. Oracle* | 593 U.S. 1 (2021) | Transformative fair use |
| *Warhol v. Goldsmith* | 598 U.S. 508 (2023) | Narrowing of transformative use |
| *Feist v. Rural Telephone* | 499 U.S. 340 (1991) | Facts not copyrightable |
| *Fair Housing v. Roommates.com* | 521 F.3d 1157 (9th Cir. 2008) | Content provider vs. platform |
| *Kimzey v. Yelp!* | 836 F.3d 1263 (9th Cir. 2016) | Section 230 for rating aggregation |
| *Lachaux v. Independent Print* | [2019] UKSC 27 | UK serious harm threshold |
| *Google v. Defteros* | [2022] HCA 27 | Australian publisher liability |
| *Von Hannover v. Germany (No. 2)* | [2012] ECHR 228 | Privacy vs. public interest balancing |

---

## Appendix B: Pre-Launch Legal Checklist

- [ ] Incorporate entity (Delaware C-Corp or California LLC)
- [ ] Engage First Amendment / media law counsel
- [ ] Draft and review ToS, Privacy Policy, Cookie Policy
- [ ] Conduct DPIA for GDPR compliance
- [ ] Register DMCA agent with Copyright Office
- [ ] Obtain media liability insurance
- [ ] Obtain E&O insurance
- [ ] Build and document dispute/appeals process
- [ ] Build public figure determination process with documentation
- [ ] Implement GDPR data subject request handling
- [ ] Implement CCPA consumer request handling
- [ ] Prepare C&D response template (counsel-reviewed)
- [ ] Establish litigation hold procedures
- [ ] Create transparency report template
- [ ] Engage UK/EU counsel before serving those markets
- [ ] Review AI Act compliance requirements
- [ ] Establish advisory board for political neutrality
- [ ] Document methodology in publishable format
- [ ] Implement confidence scoring with minimum thresholds
- [ ] Test scoring system against edge cases (satire, humor, hyperbole)

---

*This document is a working legal analysis, not legal advice. It should be reviewed by qualified counsel in each relevant jurisdiction before any business decisions are made based on its contents.*
