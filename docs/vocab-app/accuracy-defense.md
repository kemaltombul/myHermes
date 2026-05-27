# Persona: Accuracy

> **Motto:** "If the numbers lie, the algorithm fails. Precision is not optional."

## Core Thesis

Most vocabulary apps fail because their scoring is too naive. A multiple-choice guess is not the same as recall from memory. The system must distinguish **recognition** (tap-to-translate) from **recall** (answering from memory) and assign different weights.

## Algorithm Proposal

**True Bayesian ELO (3-factor):**

Three separate scores per word:

| Factor | Weight | How it's measured |
|--------|--------|-------------------|
| Recognition (R) | 30% | Tap-to-translate accuracy (easy, fast, low stakes) |
| Recall (K) | 50% | Typed/written answers from memory (hard, slow) |
| Comprehension (C) | 20% | Reading quiz answers (contextual understanding) |

Final word ELO = 0.3×R + 0.5×K + 0.2×C

Each factor uses proper Bayesian updating:
- Prior: Beta(2, 2) — assumes 50% chance on first try
- Update on answer: Beta(α + correct, β + incorrect)
- ELO estimate = (α / (α + β)) × 2000

This means a word with 1 correct + 2 wrong = fairer rating than raw percentage.

**Reading question selection (adaptive):**
- Use Item Response Theory (IRT): each question has a difficulty parameter
- If user's word-ELO > question-difficulty, show harder follow-ups
- If user gets it wrong, show easier questions
- This is a 1-parameter Rasch model — simple enough to implement

**Why this beats alternatives:**
- Separates "knows the word" from "guessed right in context"
- Bayesian prior prevents cold-start problems
- IRT ensures reading questions are always at the right level
- No arbitrary K-factors or magic numbers

## Concessions

- Most complex to implement (~200 lines for the core)
- Beta distribution math needs a small stats library
- Users won't "feel" the difference unless they study 50+ words
- Overkill for <100 word vocab collections

## Verdict

**Build it right or don't build it at all.** A wrong ELO score is worse than no score — it actively misleads the spaced repetition.