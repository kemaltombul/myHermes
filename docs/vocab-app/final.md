# Final Decision: Vocab App ELO & Retrieval Algorithm

> **Date:** 2026-05-27
> **Discussion:** 5 personas debated — Efficiency, Consistency, Accuracy, Simplicity, UX
> **Model:** DeepSeek v4Pro reasoning

---

## Core Architecture Decision

**Hybrid model — Simplicity-first, Accuracy-aware, UX-driven.**

Not a single ELO number. Two scores per word:

| Score | Range | What it measures | Update rule |
|-------|-------|------------------|-------------|
| **Skill (S)** | 0.0–1.0 | Recall + comprehension accuracy | Bayesian Beta(α,β) |
| **Priority (P)** | integer | Days since last review × wrong count | SM-2 intervals |

`word_elo` is derived: `ELO = S × 2000` (display only, not stored).

## Why this hybrid?

- **Efficiency ✅** — O(1) retrieval per word, no joins, no cron
- **Consistency ✅** — Bayesian Beta update smooths curves, SM-2 intervals are proven
- **Accuracy ✅** — Recognizes the difference between a lucky guess and real knowledge
- **Simplicity ✅** — No K-factors, no multi-factor ELO, no Rasch models in v1
- **UX ✅** — ELO is a progress bar derived from S, gamified with streak bonuses

---

## Scoring Rules (detailed)

### Skill Score (S) — Bayesian Beta

- Prior: `Beta(α=2, β=2)` → S ≈ 0.5 (50% confidence on first try)
- Correct answer in recall: `α += 1`
- Wrong answer in recall: `β += 1`
- Correct in reading comprehension: `α += 1` (same weight as recall — comprehension IS recall)
- Tap-to-translate: no update (this is practice, not measurement)
- Derived S = `α / (α + β)`

### Priority Score (P) — SM-2 Lite

- `next_review` calculated from S:
  - S < 0.3 → next session (same day)
  - 0.3 ≤ S < 0.6 → 1 day
  - 0.6 ≤ S < 0.8 → 3 days
  - 0.8 ≤ S < 0.95 → 7 days
  - S ≥ 0.95 → 30 days (maintenance mode)
- If user gets it wrong: next_review = today (reset), α/β reset to Beta(2,2) (fresh start)
- Query: `SELECT * FROM words WHERE next_review <= NOW() ORDER BY S ASC`

### Daily Session Composition

- **70%** algorithm picks (due words, lowest S first)
- **20%** algorithm picks (recently added words, first 3 reviews accelerated: next_review = same session)
- **10%** random surprise words (keeps it fresh, UX req)

### Reading Comprehension Questions

- Agent fetches BBC news → splits text by sentence
- Questions are generated per sentence (not random)
- Each question tests a specific word/phrase from the text
- On correct answer: both that word S up + user's overall reading comprehension bonus
- Questions are adaptive: if user gets 2 wrong in a row, next question targets easier words from the same text

---

## Gamification Layer (UX)

- **Streak bonus:** 3+ correct recall in a row → visual +12 animation instead of +8 (S update still honest, only display is boosted)
- **Comeback mercy:** wrong after long streak → display shows −4 (S is still −β, but toast is gentle)
- **ELO meter:** progress bar from 0 → 2000, animated
- **Confetti:** after 3 correct reading answers in one session

---

## Implementation Priority

| Phase | What | Why |
|-------|------|-----|
| **v0.5** | S as float (0.0–1.0), simple ±0.15/-0.25 | Ship fast, validate UX |
| **v1.0** | Bayesian Beta update + SM-2 intervals | Real algorithm, still simple |
| **v1.5** | Reading comprehension → S update | Connect page 3 to algorithm |
| **v2.0** | Gamification + streak tracking | Retention optimization |

**Start with v0.5.** The user needs to feel the app before tuning ELO.

---

## ELO ≠ English Level

As the user noted: **word ELO is not the same as user English ELO.**

- Word ELO = how well the user knows *that specific word*
- User English ELO = aggregate across all words (~average S × 2000)
- These are separate concepts. The app tracks word ELO per vocab item. User ELO is a derived dashboard stat.

---

## Open Questions (deferred)

1. **Multi-word phrases** — should they share a single S or decompose to individual words?
   - *Decision:* Phrases get their own S. If a phrase contains words already in the vocab, the phrase score is independent. No cross-contamination.
2. **Reading text vs. vocab collection overlap** — if a word from page 3 was already in the vocab, do we update the existing S or create a separate one?
   - *Decision:* Same word = same record. Tap-to-save from reading adds to the shared vocab collection. S updates apply to the shared ELO.
3. **ELO decay** — should unused words decay?
   - *Decision:* Not in v1. SM-2 intervals handle this with longer wait times. Add decay (S -= 0.02 per week without review) in v2 if needed.

---

## Decision Summary

| Aspect | Chosen approach | Rejected alternatives |
|--------|----------------|----------------------|
| Scoring | Bayesian Beta (α,β) | Raw +/-, TrueSkill, plain ELO |
| Retrieval | SM-2 Lite intervals | Session-count proxy, random |
| Weights | Recall = Comprehension = 1×, Translation = 0 | 3-factor weighted ELO |
| Display | Progress bar from S×2000 | Raw ELO number |
| Gamification | Streak display boost, comeback mercy | Pure scores only |
| v1 target | Ship v0.5 fast, iterate to v1.0 | Build perfect system first |