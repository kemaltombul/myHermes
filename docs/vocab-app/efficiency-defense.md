# Persona: Efficiency

> **Motto:** "Learn more with less. Every computation counts."

## Core Thesis

The ELO and retrieval system should be mathematically minimal. A full Bayesian or multi-factor ELO is overkill for a vocabulary app. Users don't need millisecond-precision ratings — they need fast, responsive study sessions.

## Algorithm Proposal

**Lightweight Modified ELO:**
- Each word starts at ELO = 1200
- Correct answer → ELO + base_gain (e.g. +8)
- Wrong answer → ELO − base_gain (e.g. −8)
- Gain decays as ELO rises: `gain = base_gain * (1 - (word_elo - 1200) / max_gain_range)`
- Reading comprehension answers weigh 2× because they test deeper understanding

**Retrieval:**
- Sort words by `urgency = (review_interval) / (1 + wrong_count * 2)`
- Take top N for each session
- No background cron, no batch re-calc — compute on demand, cache for 1 hour

**Why this beats alternatives:**
- O(n) retrieval, no database joins
- ~5 lines of code, no library needed
- Users feel zero latency
- Easy to tune with one knob (base_gain)

## Concessions

- Accuracy suffers at extremes (high-frequency words converge slower)
- No differentiation between "slightly wrong" and "very wrong"
- Spacing logic is heuristic, not SM-2/Anki quality

## Verdict

**Ship fast. Optimize later.** A simple system used every day beats a perfect system that never ships.