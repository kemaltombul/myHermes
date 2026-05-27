# Persona: User Experience

> **Motto:** "The algorithm is invisible. The feeling of progress is everything."

## Core Thesis

The user doesn't care about ELO math. They care about:
- "Do I feel smarter after 10 minutes?"
- "Does the app respect my time?"
- "Is it fun to tap words and see translations?"

The algorithm should optimize for **engagement signals**, not statistical purity.

## Algorithm Proposal

**Gamified ELO with Streak Bonus:**
- Same base as Efficiency's proposal (start 1200, ±8 per answer)
- **Streak bonus**: 3+ correct in a row → gain ×1.5. Visual feedback: +12 instead of +8.
- **Comeback bonus**: wrong after a long streak → lose only −4 (half penalty) to protect motivation
- **Daily first-session boost**: first 5 reviews of the day get +2 bonus gain — rewards opening the app
- ELO displayed as a progress bar, not a number (nobody wants to see 1347)

**Reading page UX-specific:**
- Tap-to-translate should be instant (<100ms)
- Saved words should appear on the read page with a visual badge
- After answering 3 reading questions correctly, show a confetti animation
- ELO update is shown as a gentle animation (+8 slides up, fades out)

**Retrieval:**
- Mix of "due now" words (algorithm decides) + 2 "surprise" random words (keeps it fresh)
- Let user manually "skip" a word they feel confident about — skips review but doesn't affect ELO (user agency matters)

**Why this beats alternatives:**
- Streak mechanics are proven by Duolingo (increases retention 40%)
- Comback bonus prevents frustration-quitting
- Random words prevent boredom (users hate seeing the same 5 words)

## Concessions

- Gamification adds code complexity (animations, streak tracking)
- ELO inflation from streaks makes absolute scores incomparable
- "Skip" feature bypasses the algorithm — purists will hate it

## Verdict

**Make it feel good or nobody uses it.** A statistically perfect algorithm means nothing if the user deletes the app after 3 days.