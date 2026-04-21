# Refresher Notes

A personal wiki builder for people who re-read their own notes weeks after
writing them. Built in ~4 hours for the Bolt product exercise.

## The Problem

When I come back to a topic after a break — a paper I half-read, a problem
set I paused — I pay a hidden tax. My notes (when I have them) are usually
the textbook's phrasing, not mine, so re-reading them is no cheaper than
re-reading the source. And the source itself is dense and non-linear, so I
end up scanning the whole PDF again to find the two paragraphs that mattered.

The cost scales with how long I was away. For a multi-week research project
this adds up to hours.

## The Insight

The standard AI move here is "summarize this PDF." That produces generic
summaries that read like everyone else's summaries, which future-me finds
no easier to digest than the original.

What I actually want is **AI as a translation layer into my own idiolect**:
feed it a sample of how I already write, and have it re-express new material
in that voice, structured for refresher-reading rather than first-time
learning.


## What's In This Repo

- `SYSTEM_PROMPT.md` — the full prompt. This is the product.
- `VOICE_PROFILE.md` — sample of my own writing, used by the prompt to
  calibrate register and vocabulary. Replace with your own to adapt.
- `INDEX.md` — running list of existing note titles and one-line
  descriptions. The prompt uses this to propose links to notes that
  already exist rather than inventing new ones.
- `examples/` — real outputs from real sources. Start here if you want
  to see what the system actually produces.

## How To Set It Up

**One-time setup (~5 minutes)**

1. Go to [claude.ai](https://claude.ai) and open **Projects** in the
   left sidebar. Create a new project — name it something like
   "Refresher Notes."
2. Inside the project, open **Project Instructions** and paste the full
   contents of `SYSTEM_PROMPT.md`. This is Claude's standing brief for
   every conversation in the project.
3. Open **Project Knowledge** and upload two files:
   - `VOICE_PROFILE.md` — first, replace the prose sample with a few
     paragraphs of your own writing. See the file for guidance.
   - `INDEX.md` — start with an empty notes list and add entries as
     your vault grows.
4. Download and install [Obsidian](https://obsidian.md) (free). On first
   open, create a new vault — point it at a folder on your disk like
   `~/Documents/ResearchVault`. That folder is your vault.

**Per-note workflow**

1. Open a new chat inside the project.
2. Upload a PDF or paste source text.
3. Claude returns a structured markdown note. Copy it.
4. Paste it into a new `.md` file inside your Obsidian vault folder.
   Name the file to match the note's title exactly
   (e.g. `Exactly Once Semantics.md`).
5. Open `INDEX.md`, add one line for the new note, re-upload it to
   Project Knowledge replacing the old version.

No code. No integration. The `[[wiki-link]]` syntax the prompt generates
is the integration — Obsidian resolves links automatically when the files
sit in the same vault folder. Drop enough notes in and the graph view
builds itself.

## Maintaining INDEX.md

Every time you add a note, add one line to INDEX.md:

```markdown
- **Note Title** — one sentence describing what it covers
```

Then re-upload it to Project Knowledge. This is the only manual
maintenance the system requires. It takes about 30 seconds per note.

**Honest limitation:** INDEX.md as a plain text list stops scaling
gracefully past ~100 notes — at that point it starts consuming too much
context window. The fix is embeddings-based retrieval so Claude sees only
the relevant subset of existing notes per query. Out of scope for this
build but the natural next step.

## What I Deliberately Left Out

- **Auto-ingestion into Obsidian.** Would have taken most of the 4 hours
  and wouldn't have changed output quality — only the friction of moving
  it around. Copy-paste is fine at this scale.
- **Embeddings-based retrieval over existing notes.** INDEX.md as a plain
  text list works for a few dozen notes. A vector store would fix the
  scaling limit but adds infrastructure without changing the output.
- **Image, equation, and table handling.** The prompt drops or mangles
  these. Fixable, but not the interesting problem for this build.
- **Multi-document synthesis.** One source → one note. The system won't
  weave three papers together.
- **Feedback loop.** The prompt doesn't learn from which notes get
  re-read vs. ignored. Would be the most useful thing to add next.

## What Doesn't Work Yet

- INDEX.md has to be updated manually after each note. Obvious
  automation target but keeping the prompt logic as the thing under
  test was the right call for a 4-hour build.
- Voice matching is only as good as the voice sample. A few paragraphs
  catches register but misses domain-specific phrasing quirks — the
  profile improves over time as you add more samples.
- Obsidian link case-sensitivity varies by OS. The prompt normalizes
  to Title Case to sidestep this, but it's a convention not a guarantee.

## Why A Prompt, Not An App

I scoped this as a prompt-and-project setup rather than a coded pipeline
because the interesting problem was the transformation logic, not the
plumbing. The prompt is ~120 lines and represents maybe 80% of the design
thinking; the rest of a "real" product would be auth, storage, a UI, and
integration glue — none of which would change what the output looks like.

That scoping decision is itself part of what I wanted to demonstrate.
