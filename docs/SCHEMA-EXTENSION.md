# Critical Thinking idea-extraction — schema proposal

## 1. What's in the folder (feedback)

The `critical-thinking/` repo is already much further along than a raw transcript dump — this isn't
a "first pass" folder, it's a near-finished Phase 0 with Phase 1 scaffolding already written.

**What's genuinely good, no changes needed:**
- 193/199 transcripts, consistently named `NNN-title.txt`, each with a 3-line header (episode #,
  title, source URL) and a `_manifest.tsv` that matches the files on disk. Clean to `grep`/feed to
  an LLM as-is.
- `links/` — 1,255 URLs already deduplicated and split into 9 categories (X profiles, HN reports,
  GitHub, blogs, etc). This is usable right now and doesn't need touching for the idea-extraction
  work.
- `docs/METHODOLOGY.md` is honest about provenance: 166 official transcripts (timestamped, speaker
  labels) vs 27 YouTube auto-caption transcripts (no punctuation/speaker labels, flagged as
  lower-trust) vs episodes 1–6 (no transcript exists anywhere, audio only). That quality signal
  matters for extraction — see below.
- `docs/TAXONOMY.md` and `docs/SUMMARY-TEMPLATE.md` already define an **episode-level** tagging
  scheme and a one-page summary template with sections for TL;DR, key techniques, tools, notable
  claims, agent/automation ideas, follow-ups, related episodes. This is a strong starting point —
  I'm not proposing a replacement, just an extension (section 2).
- `state/events.jsonl` is the build tool-call trace from scraping the archive. Not source material,
  just a build log — safe to ignore for extraction.

**Gaps / things worth fixing before running extraction at scale:**
1. **No stable idea ID.** The current "Agent & automation ideas" section in `SUMMARY-TEMPLATE.md`
   is prose bullets (Idea / Why / Evidence / Effort) with no identifier. That's fine for a human
   reading one file, but the downstream pipeline (dedup → merge → gap-analysis → implement) needs
   to reference *this specific idea from this specific episode* unambiguously, including after
   it's been merged with 3 other episodes' version of the same idea. Prose bullets alone can't
   carry that. → proposed fix in section 2.
2. **Episode-level tags ≠ idea-level category.** `TAXONOMY.md`'s tags (`ai-agents`,
   `web-client-side`, etc.) answer "what is this *episode* about" — great for the topic indexes in
   Phase 2. They don't answer "what part of *our agent* does this *idea* touch" (a skill? a pipeline
   agent file? a schema change? a standalone tool?) — which is exactly what the gap-analysis pass
   needs to route each idea correctly. These are two different axes and both are needed.
3. **No quality/confidence carry-through.** YouTube-caption episodes (27 of them, no speaker labels,
   occasional mis-hearings) should flow into extraction with a lower confidence flag so a sketchy
   claim doesn't get merged in with the same weight as a timestamped official-transcript claim.
   Nothing in the current template captures that.
4. **`notes/` is empty** (expected — Phase 1 hasn't started) and there's no `ideas/` folder yet
   (expected — Phase 3). Nothing wrong here, just flagging that the schema below is designed to feed
   both once they exist.

Bottom line: the archive itself needs no rework. The template needs a small, additive extension —
not a rewrite — to carry idea IDs, agent-category, and confidence through to the merge/gap/implement
passes.

---

## 2. Proposed schema (extends `SUMMARY-TEMPLATE.md`, doesn't replace it)

Keep everything in the current template. Add structure to exactly one section —
**"Agent & automation ideas"** — plus one new frontmatter field.

### 2.1 Frontmatter (add one field to the existing block)

```yaml
---
episode: 123
title: "Episode title"
tags: [ai-agents, web-server-side]        # existing — TAXONOMY.md vocabulary, episode-level
primary_tag: ai-agents                     # existing
source: "https://www.criticalthinkingpodcast.io/..."
transcript: "../transcripts/123-title.txt"
transcript_quality: official | youtube-caption | none   # NEW — carries METHODOLOGY.md's trust signal
---
```

### 2.2 Idea records (replaces the loose bullets under "Agent & automation ideas")

Each idea becomes a small structured block instead of a free bullet:

```markdown
## Agent & automation ideas

### IDEA-123-01: Judge/verifier-as-a-hook with a rubric loop
- **category:** agent            <!-- skill | agent | pipeline | tooling | ops | other -->
- **targets:** verifier.md       <!-- our file/skill this would touch, best guess, optional -->
- **why:** [one sentence — the problem it solves]
- **evidence:** [episode timestamp or transcript search term]
- **effort:** small | medium | large
- **confidence:** high | medium | low   <!-- derived from transcript_quality + how concrete the claim was -->

### IDEA-123-02: ...
```

**Field notes:**

- **Idea ID = `IDEA-<episode>-<NN>`**, two-digit, sequential within the episode, assigned once and
  never reused. This is the stable handle every later pass references — dedup records which IDEAs
  got merged, gap-analysis tags the merged result, implementation PRs cite it. It never changes
  even if the idea's title/wording is edited later.
- **`category`** is a *closed* 6-value enum, chosen to map 1:1 onto how our agent is actually
  organized, so gap-analysis can route mechanically instead of re-deciding category for every idea:
  - `skill` → a new or improved entry in the ~400-skill library
  - `agent` → a change to one of the 13 pipeline agent files (orchestrator/recon/hunter/verifier/…)
  - `pipeline` → a schema/engine-level change (coverage ledger, lead routing, VulnFamily, etc.)
  - `tooling` → a change to `my-tools/` (subleech/jsleech/gitleech/tiqhound) or a new wrapped tool
  - `ops` → account pool, mailbox provider, identity management, non-security plumbing
  - `other` → doesn't fit cleanly yet; don't force it, this is the known escape hatch (same pattern
    used for `VulnFamily.other` in the main agent)

  This is a separate axis from `tags`/`primary_tag` (episode topic). An episode tagged
  `web-client-side` can easily produce an idea categorized `skill` (a new CSS-injection skill) *and*
  an idea categorized `pipeline` (a routing change) in the same note.
- **`confidence`** exists because 27 episodes are uncorrected YouTube captions. A "high effort,
  low confidence" idea should get a sanity-check pass before it's trusted in the merged library;
  a "low effort, high confidence" idea is close to shovel-ready. This isn't a numeric threshold —
  it's a label the gap-analysis pass reads, not a cutoff I'm inventing for you.
- **`targets`** is optional and a best guess only (the extraction pass is reading an episode, not
  the current agent state) — gap-analysis is what actually confirms already-have/partial/missing
  against the real code.

### 2.3 What gap-analysis adds later (not part of per-episode extraction — documented here so the
IDs above are future-proofed for it)

```markdown
### LIB-014: Judge/verifier-as-a-hook with a rubric loop
- **source_ideas:** [IDEA-123-01, IDEA-045-03, IDEA-166-02]   <!-- merged from 3 episodes -->
- **category:** agent
- **status:** partial          <!-- already-have | partial | missing -->
- **status_note:** verifier.md re-checks already, but no rubric-scored loop
- **priority:** P1             <!-- P0-P3, ordinal ranking, not a numeric score -->
```

`LIB-NNN` is the merged/canonical ID in the Phase 3 `ideas/BACKLOG.md`. It always carries its
`source_ideas` backrefs so you can trace any implemented feature back to the exact episode segment
that inspired it — same "grounded in the source" principle your own `ROADMAP.md` already states as
Principle 1.

This is additive to your existing docs: `TAXONOMY.md` and the rest of `SUMMARY-TEMPLATE.md` stay
exactly as written. Only the ideas section gets structure, and one frontmatter field gets added.

---

## 3. Proposed next move

Your 5-pass plan is right and I'm not changing it. Concretely:

1. **Files** — already done (193 transcripts in place, well-formed).
2. **Per-episode extraction** against the schema in section 2 → one file per episode in `notes/`,
   following `SUMMARY-TEMPLATE.md` plus the `IDEA-NNN-NN` blocks.
3. **Dedup/merge** — all `IDEA-*` blocks across every note get clustered by similarity into
   `LIB-NNN` records in `ideas/BACKLOG.md`, each keeping its `source_ideas` backrefs.
4. **Gap-analysis vs agent** — each `LIB-NNN` gets `status` + `priority` by checking the live
   `opencode/` agent/skill state (not by re-deciding category — `category` is already set in step 2).
5. **Implement** — prioritized batches, back in the main `hexxmate_agent` project thread.

**What I'd want you to confirm before I run step 2 at scale (199 episodes is a lot of tokens and
re-running is expensive):**

- **The schema in section 2** — specifically the 6-value `category` enum and whether `effort`/
  `confidence`/`priority` as ordinal labels (not numbers) is the right call, same as your standing
  rule against inventing numeric defaults.
- **Batch order** — run oldest→newest, or prioritize by density (e.g. the episodes you/predecessor
  already flagged as idea-rich: 008, 039, 059, 101, 102, 123/126/117 "Vulnus ex Machina" AI-hacking
  series, 128, 134, 137, 148, 152, 165, 166, 182, 192)? Doing the AI-agent/architecture-heavy
  episodes first would surface the highest-value ideas soonest.
- **Review gate** — do AI-drafted summaries get a human skim before they're considered "merged" into
  `notes/`, per `CONTRIBUTING.md`'s own guideline ("review AI-drafted summaries for accuracy before
  merging")? If yes, what's the review unit — per episode, or per batch of N?
- **Low-confidence episodes (the 27 YouTube-caption ones + any with a sparse/garbled transcript)** —
  extract them in the same pass with `confidence: low` flagged, or hold them for a second pass after
  the official-transcript episodes are done?

Say go (with any adjustments to the above) and I'll start the per-episode pass.
