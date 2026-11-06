# Roadmap

## Vision

Turn 199 episodes of practical bug bounty knowledge into a **structured, searchable, agent-ready
corpus** — one that helps humans find techniques quickly and helps AI agents acquire concrete,
grounded security knowledge and implementation ideas.

The archive is the foundation. The real product is what gets built on top of it: summaries,
topic indexes, and a curated backlog of skills, tools, and automations.

## Principles

1. **Grounded in the source.** Every summary and idea links back to an episode.
2. **Multi-label, not multi-folder.** Episodes cover several topics; tagging beats moving files.
3. **Useful over exhaustive.** A one-page summary that is read beats a 20-page summary that is not.
4. **Agent-ready.** Notes should be structured enough to feed to an LLM without preprocessing.
5. **Attribution first.** Transcripts and linked content belong to their owners.

## Phases

### Phase 0 — Archive ✅

- [x] Enumerate all 199 episodes.
- [x] Download and normalize 193 available transcripts (166 official, 27 YouTube captions).
- [x] Extract 1,255 links from all episode show notes, categorized.
- [x] Build episode index and manifest.
- [x] Publish the repository with documentation and contribution guidelines.

**Output:** `transcripts/`, `links/`, `docs/EPISODE-INDEX.md`.

### Phase 1 — Episode summaries ⏳

- [ ] Write a structured summary for every episode (`notes/NNN-title.md`).
- [ ] Use [`SUMMARY-TEMPLATE.md`](SUMMARY-TEMPLATE.md) for consistency.
- [ ] Prioritize episodes with the highest density of techniques and tools.
- [ ] Review AI-drafted summaries for accuracy before merging.

**Definition of done:** every episode has a summary containing a TL;DR, key techniques, tools,
notable claims, and follow-up links.

### Phase 2 — Taxonomy and indexes 📋

- [ ] Finalize the tag vocabulary in [`TAXONOMY.md`](TAXONOMY.md).
- [ ] Tag every summarized episode with multiple topics.
- [ ] Generate per-topic indexes (one Markdown file per tag) from the tags.
- [ ] Add a "start here" reading path for newcomers.
- [ ] Add an index of guests and their episodes.

**Output:** `indexes/<topic>.md`, generated from tags — files never move.

### Phase 3 — Idea extraction 📋

- [ ] Mine summaries for reusable ideas:
  - agent skills and tool definitions,
  - automation and workflow opportunities,
  - agent architecture patterns,
  - testing methodologies that can become checklists,
  - datasets, wordlists, and payload collections.
- [ ] Score ideas by impact, effort, and evidence strength.
- [ ] Publish a prioritized backlog in `ideas/`.
- [ ] Convert the strongest ideas into implementable specs.

**Output:** `ideas/BACKLOG.md` plus one spec per accepted idea.

### Phase 4 — Agent-ready packs 📋

- [ ] Package selected ideas as skill definitions, prompts, or knowledge files.
- [ ] Add evaluation notes: what the skill should do, failure modes, and test cases.
- [ ] Keep the packs vendor-neutral so they work with different agent frameworks.
- [ ] Publish a versioned release.

**Output:** `packs/` or a linked companion repository.

### Phase 5 — Maintenance 📋

- [ ] Add new episodes as they are released.
- [ ] Re-check links quarterly and replace dead ones with archived copies.
- [ ] Keep the manifest and episode index in sync.
- [ ] Review open issues and contributions monthly.

## Non-goals

- Replacing the official podcast, transcripts, or show notes.
- Redistributing paywalled or private material.
- Presenting AI-generated summaries as official statements from the hosts.
- Building a tool that attacks systems; this is a knowledge archive, not an exploit kit.

## Success criteria

- A newcomer can find every episode about a given topic in under a minute.
- Every transcript is searchable and every summary traces back to its episode.
- The idea backlog contains concrete, implementable proposals — not vague notes.
- The repository stays useful with zero maintenance for months at a time.

## How to help

Pick any unchecked item and open an issue or a pull request. Small, steady contributions are more
valuable than a large rewrite. See [`../CONTRIBUTING.md`](../CONTRIBUTING.md).
