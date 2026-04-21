# Voice Profile

> This file is user-specific. The prose sample below belongs to the repo
> author — replace it with your own writing to get genuine voice matching.
> The style notes in the "Meta Description" section are there to guide you
> on what to write about, not to replace the prose sample. The more your
> sample sounds like how you actually think, the better the output.

---

## Prose Sample

CoSyntax tackles a simple but annoying problem: sharing code snippets between
friends is clunkier than it needs to be. The inspiration came mostly from
leetcoding with friends, where getting guidance in natural language is often
harder to follow than just seeing the actual edits in the code. The idea was
basically "Google Docs for code" — multiple people connecting to the same
session and seeing each other's changes live.

On the technical side it's a JavaScript-heavy fullstack app. Next.js and
Node.js handle the frontend and backend, websockets keep everyone in sync in
real time, and CRDTs make sure each user can edit their own local instance
without conflicts before it gets reflected in the global state. There's
database support too, mostly for testing so users can save snippets if they
want. Autocomplete runs through the OpenAI API, though that requires a valid
key. Scaling isn't really a concern — the whole thing is designed for small
groups, not production traffic.

---

## Meta Description

Use the section below to understand the register and rules the prose sample
demonstrates. When writing your own sample, aim for the same qualities.

### Register
Casual but precise. Explain things the way you would to a smart friend
who has roughly your background — not a professor, not a five-year-old.
Contractions are fine. Technical jargon is fine when it's the right word;
swap it out when a simpler word exists.

### Sentence style
Short to medium sentences. Mix of prose and bullets depending on whether
the content flows as an argument or breaks naturally into a list. Avoid
walls of text. Transitions should be natural, not formal.

### Technicality level
Fill this in for yourself. Examples:
- "CS undergrad, comfortable with distributed systems and data pipelines,
  weaker on statistics formalism — prefer intuition over notation."
- "Self-taught, strong in frontend, shakier on systems-level concepts."
The system uses this to calibrate how much it explains vs. assumes.

### Deliberately avoid
- Filler openers ("It's worth noting that...", "In this note we will...")
- Over-hedging ("this might possibly suggest that perhaps...")
- Passive voice where active works fine
- Formal transitions ("Furthermore", "Moreover", "In conclusion")
- Longer words when shorter ones exist ("utilize" → "use",
  "demonstrate" → "show")

### What good output looks like
Re-read your prose sample. The system's output should feel like it could
have been written by the same person in the same sitting. If the output
sounds more formal, more hedged, or more generic than your sample —
the sample probably needs more personality, or the technicality level
needs adjusting.