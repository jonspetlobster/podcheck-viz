# PodCheck — Prompt Testing Plan

---

## 1. Evaluation Metrics

### Claim Extraction Metrics

| Metric | Definition | Target |
|--------|-----------|--------|
| **Precision** | Claims extracted that ARE real claims / total extracted | ≥ 90% |
| **Recall** | Real claims extracted / total real claims in transcript | ≥ 85% |
| **False Positive Rate** | Non-claims incorrectly extracted / total extracted | ≤ 10% |
| **Humor Detection Accuracy** | Jokes correctly skipped / total jokes in transcript | ≥ 90% |
| **Category Accuracy** | Claims assigned correct category / total claims | ≥ 95% |
| **Verifiability Rating Accuracy** | Ratings matching human judgment / total claims | ≥ 80% |

### Claim Verification Metrics

| Metric | Definition | Target |
|--------|-----------|--------|
| **Verdict Accuracy** | Verdicts matching human baseline / total verified | ≥ 85% |
| **Verdict ±1 Accuracy** | Verdicts within 1 step of human baseline (e.g., True vs Mostly True) | ≥ 95% |
| **Confidence Calibration** | Correlation between stated confidence and actual accuracy | r ≥ 0.7 |
| **Human Review Precision** | Flagged claims that actually needed review / total flagged | ≥ 70% |
| **Human Review Recall** | Claims needing review that were flagged / total needing review | ≥ 95% |

### Cost Metrics

| Metric | Target |
|--------|--------|
| **Cost per episode (60 min)** | ≤ $0.50 (v1), ≤ $2.00 (v2), ≤ $5.00 (v3) |
| **Latency per segment (3 min)** | ≤ 3s (v1), ≤ 8s (v2), ≤ 15s (v3) |
| **Claims per episode** | Track average to detect prompt drift |

---

## 2. Test Suite: 20 Transcript Snippets

### Group A: Should Contain Claims (10 snippets)

**A1 — Simple statistic**
```
[00:05:12] HOST: The US population just crossed 340 million people.
```
Expected: 1 claim (statistical, high verifiability)

**A2 — Scientific claim with source**
```
[00:11:30] DR. KIM: A 2022 study in The Lancet found that moderate alcohol consumption — even one drink per day — increases cancer risk by 8%.
```
Expected: 1 claim (scientific, high verifiability)

**A3 — Historical fact**
```
[00:08:45] GUEST: The iPhone was announced by Steve Jobs in January 2007 at Macworld.
```
Expected: 1 claim (historical, high verifiability)

**A4 — Causal claim**
```
[00:15:22] PROF. LIU: Increased screen time in children directly leads to higher rates of myopia. The research is very clear on this.
```
Expected: 1 claim (causal/scientific, medium verifiability — "directly leads to" is strong causal language)

**A5 — Multiple claims in one segment**
```
[00:20:10] ANALYST: Amazon's AWS revenue hit $90 billion in 2023, making up about 70% of their operating profit. Meanwhile, Google Cloud is still losing money — they lost about $3 billion last year.
```
Expected: 3 claims (AWS revenue, profit share, Google Cloud losses — all statistical, high verifiability)

**A6 — Claim with hedging**
```
[00:33:15] NUTRITIONIST: I think — well, actually it's established — that vitamin D deficiency affects roughly 42% of American adults. That's from a National Health survey.
```
Expected: 1 claim (statistical, high verifiability, contains_hedging: true)

**A7 — Attribution claim**
```
[00:41:00] HOST: Didn't the WHO say that sitting is the new smoking?
```
Expected: 1 claim (attribution/historical, high verifiability — verifiable whether WHO actually said this)

**A8 — Claim buried in casual speech**
```
[00:27:44] JOE: Yeah man, it's crazy. So apparently like the average American eats about 3,400 milligrams of sodium per day, which is way over the recommended limit.
```
Expected: 1 claim (statistical, high verifiability — specific number despite casual framing)

**A9 — Tech claim**
```
[00:55:02] GUEST: GPT-4 was trained on roughly 13 trillion tokens. That's public information from their technical report.
```
Expected: 1 claim (factual, high verifiability — references specific source)

**A10 — Finance claim with context**
```
[00:38:20] ECONOMIST: During the 2008 financial crisis, the Fed's balance sheet expanded from about $900 billion to over $2 trillion in just a few months.
```
Expected: 1 claim (statistical/historical, high verifiability)

---

### Group B: Should NOT Contain Claims (10 snippets)

**B1 — Pure opinion**
```
[00:12:05] HOST: I honestly think remote work is the best thing that ever happened to the tech industry.
```
Expected: 0 claims (opinion + hyperbole)

**B2 — Joke with factual-sounding content**
```
[00:19:30] COMEDIAN: [laughs] Yeah, 99% of statistics are made up on the spot. Including that one.
```
Expected: 0 claims (obvious joke, self-referentially ironic)

**B3 — Sarcasm**
```
[00:25:15] GUEST: Oh sure, because eating nothing but kale for a month definitely cured my uncle's back pain. [laughs] That's how science works, right?
```
Expected: 0 claims (sarcasm — speaker doesn't believe this)

**B4 — Prediction**
```
[00:30:00] ANALYST: I'm telling you, Bitcoin is going to $500,000 by 2030. Mark my words.
```
Expected: 0 claims (prediction, not verifiable fact)

**B5 — Hypothetical**
```
[00:44:12] PROF: If we had invested in nuclear energy in the 1980s instead of shutting plants down, we'd probably have solved climate change by now.
```
Expected: 0 claims (counterfactual hypothetical)

**B6 — Vague statement disguised as fact**
```
[00:16:45] HOST: Everyone knows that sugar is terrible for you. Like, tons of research shows it.
```
Expected: 0 claims ("everyone knows" + "tons of research" are too vague; no specific assertion)

**B7 — Rhetorical question**
```
[00:52:30] GUEST: I mean, who even reads the terms of service? Nobody, that's who.
```
Expected: 0 claims (rhetorical question + hyperbolic answer)

**B8 — Personal anecdote**
```
[00:08:20] HOST: When I went to Japan last year, I noticed the trains were always exactly on time. Like, to the second.
```
Expected: 0 claims (personal anecdote — unverifiable individual experience; the general claim about Japanese trains is implied but not explicitly stated as a factual assertion)

**B9 — Opinion disguised as fact**
```
[00:35:50] GUEST: Look, the reality is that regulation kills innovation. Every time the government steps in, things get worse.
```
Expected: 0 claims (opinion/ideology presented declaratively — "regulation kills innovation" is a value judgment, "every time" is unfalsifiable hyperbole)

**B10 — Technically true but presented as joke**
```
[00:29:00] HOST: Fun fact — a day on Venus is longer than a year on Venus. [laughs] Space is drunk.
```
Expected: This is a TRICKY edge case.
- v1 should skip it (joke framing, "fun fact" + laughter + "space is drunk")
- v2/v3 should extract it (it's actually a real, verifiable astronomical fact despite the humorous presentation. The "fun fact" framing signals the speaker believes it's true.)
- This tests the sophistication gap between prompt versions.

---

## 3. A/B Testing Framework

### Test Protocol

1. **Golden Set**: Maintain the 20 snippets above + expand to 100 over time. Each has human-annotated ground truth (claims, categories, verdicts).

2. **Running A/B Tests**:
   ```
   For each prompt version pair (A, B):
     For each snippet in golden set:
       Run version A → collect output
       Run version B → collect output
       Score both against ground truth
     Compare aggregate metrics
     Run statistical significance test (McNemar's test for paired binary outcomes)
     Require p < 0.05 and ≥2% improvement to adopt new version
   ```

3. **Live A/B Testing**:
   - Route 10% of production traffic to candidate prompt
   - Compare: extraction count per episode, human review override rate, user feedback scores
   - Run for minimum 200 episodes before deciding
   - Monitor for regressions in specific categories (e.g., new version worse at humor detection)

### Version Comparison Matrix

Run all 3 extraction versions + all 3 verification versions against the full test suite. Track:

| | v1-simple | v2-detailed | v3-cot |
|---|---|---|---|
| Precision | | | |
| Recall | | | |
| F1 | | | |
| Cost/episode | | | |
| Latency/segment | | | |
| Humor detection | | | |
| Edge case accuracy | | | |

---

## 4. Version Selection Guidelines

### When to use v1-simple
- High-volume processing (backlogs, batch jobs)
- Clear-cut content: news recap podcasts, interview-style with domain experts
- Budget-constrained runs
- Initial pass before escalating uncertain segments to v2/v3
- **Expected accuracy**: ~85% extraction precision, ~80% verdict accuracy
- **Cost**: ~$0.30-0.50/episode

### When to use v2-detailed
- **Default for production**
- Conversational podcasts with humor, banter, multiple speakers
- Health, finance, and science content
- When user-facing output quality matters
- **Expected accuracy**: ~92% extraction precision, ~87% verdict accuracy
- **Cost**: ~$1.00-2.00/episode

### When to use v3-chain-of-thought / v3-adversarial
- Health/medical claims that could influence listener behavior
- Politically sensitive or polarizing topics
- Claims flagged by v2 as needing human review (run v3 before human)
- High-profile podcasts (large audience, reputational risk)
- When a user disputes a verdict
- **Expected accuracy**: ~96% extraction precision, ~92% verdict accuracy
- **Cost**: ~$3.00-5.00/episode

### Cascading Strategy (Recommended)

```
1. Run v1 extraction on full transcript
2. For segments with >2 humor markers or >3 speakers → re-run with v2
3. Run v1 verification on all extracted claims
4. For claims with confidence <70% or health/political category → re-run with v2
5. For claims flagged for human review by v2 → run v3 before sending to human queue
```

This typically costs ~$0.80-1.50/episode while achieving v2-level accuracy on most claims and v3-level accuracy on the hardest ones.

---

## 5. Regression Testing

### Automated Pipeline

```bash
# Run on every prompt change
python run_test_suite.py \
  --prompt-version v2 \
  --golden-set tests/golden_set.json \
  --output results/$(date +%Y%m%d).json

# Compare against baseline
python compare_results.py \
  --baseline results/baseline.json \
  --candidate results/$(date +%Y%m%d).json \
  --threshold 0.02  # flag if any metric drops >2%
```

### Golden Set Maintenance

- Add 5-10 new snippets per month from real production transcripts
- Prioritize edge cases where the model got it wrong
- Re-annotate with 2+ human annotators; require agreement for ground truth
- Version the golden set alongside prompt versions
- Track per-category performance over time (watch for category-specific regressions)

### Prompt Drift Detection

- Log every prompt change with git
- Run full test suite before and after every change
- Track metrics weekly even without prompt changes (model updates can cause drift)
- Alert if any metric drops >5% week-over-week

---

## 6. Human Baseline

### Establishing Ground Truth

1. Recruit 3 fact-checkers (journalism background preferred)
2. Each annotates the same 50 transcripts independently
3. Measure inter-annotator agreement (target: Cohen's κ ≥ 0.8)
4. Resolve disagreements through discussion
5. Use consensus annotations as ground truth

### Ongoing Calibration

- Sample 5% of production claims weekly
- Have human fact-checkers verify independently
- Compare model verdicts to human verdicts
- Update prompts if systematic biases are detected (e.g., model consistently rates "Mostly True" when humans say "Mixed")
