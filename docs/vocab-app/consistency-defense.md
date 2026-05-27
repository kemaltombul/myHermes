# Persona: Consistency

> **Motto:** "The student should never feel lost. Predictable progress builds trust."

## Core Thesis

Learning is a long game. The algorithm must produce **stable, smooth learning curves**. If a word jumps from ELO 1300 to 1500 after one lucky guess, the system fails the user later when they overestimate their mastery.

## Algorithm Proposal

**Sigmoïd-Smoothed ELO with Decay Penalty:**
- Word ELO follows a logistic curve capped at 2000
- Update uses K-factor from chess: `K = 32` for new words, `K = 16` after 10 reviews, `K = 8` after 30 reviews
- Expected score: `E = 1 / (1 + 10^((1200 - word_elo) / 400))`
- New ELO = old + K × (actual_score − E)
- **Decay penalty**: if not reviewed in 7 days, ELO −2/day (capped at −50). This forces natural spaced repetition without hard deadlines.

**Retrieval Algorithm (SM-2 style):**
- Each interval grows: 1d → 3d → 7d → 14d → 30d
- If wrong, reset interval to 1d and drop ELO by K×2
- If ELO > 1600, mark as "reviewed" (check once per month)
- Retrieval query: `SELECT * FROM words WHERE next_review <= NOW() ORDER BY elo ASC`

**Why this beats alternatives:**
- Learning curves are smooth — no emotional whiplash
- K-factor decay prevents random fluctuation at high mastery
- Decay penalty handles absent users gracefully
- SM-2 intervals are proven in Anki (20+ years of data)

## Concessions

- More DB queries per session (need to calculate decay for all words)
- K-factor math is overkill for an MVP
- Users who study daily may feel the intervals are too conservative

## Verdict

**Predictability over speed.** A user who opens the app after 2 weeks should see exactly the words they've forgotten, not a random shuffle.