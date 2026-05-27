# Persona: Simplicity

> **Motto:** "Complexity is the enemy of completion. Make it boring. Make it work."

## Core Thesis

The user described a 3-page app. The algorithm should be the **least interesting part** of the codebase. If the ELO system needs a README to understand, it's too complicated. The user will tune it empirically anyway.

## Algorithm Proposal

**Score-based system (not ELO):**
- Each word has `score: float = 0.0` (range 0.0–1.0)
- Correct answer: `score = min(1.0, score + 0.15)`
- Wrong answer: `score = max(0.0, score - 0.25)` (mistakes hurt more than successes help)
- Reading questions: `score += 0.05` for correct comprehension (bonus only)
- Tap-to-translate: no score change (just practice)

**Retrieval algorithm — simplest possible:**
```
if score < 0.3: show every session
elif score < 0.6: show every 3 sessions
elif score < 0.8: show every 7 sessions
else: show every 30 sessions
```

No DB timestamps needed. Count sessions since last review.

**Why this beats alternatives:**
- A single float. No math library. No K-factor. No decay function.
- 10 lines of code. A junior dev can maintain it.
- The 3-tier threshold trick works well enough for 50-200 word collections
- Users understand it. "Why did this word show up?" → "Because you keep getting it wrong."

## Concessions

- Not adaptive per user — same thresholds for everyone
- Score ≠ probability of recall (psychologically inaccurate)
- No spaced repetition intervals (session-count is a poor proxy for time)
- Will feel wrong for power users (1000+ words)

## Verdict

**Build the app first. If the algorithm becomes a bottleneck, upgrade it then.** A working app with a dumb algorithm teaches you more than a perfect algorithm with no users.