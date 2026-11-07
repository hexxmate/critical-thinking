---
episode: 165
title: "Protobuf Hacking, AI-Powered Bug Hunting, and Self-Improving Claude Workflows"
tags: [ai-agents, web-server-side, web-client-side, tooling-automation]
primary_tag: ai-agents
source: "https://www.criticalthinkingpodcast.io/episode-165-protobuf-hacking-ai-powered-bug-hunting-and-self-improving-claude-workflows/"
transcript: "../transcripts/165-protobuf-hacking-ai-powered-bug-hunting-and-self-improving-claude-workflows.txt"
transcript_quality: official
---

# Episode 165 — Protobuf Hacking, AI-Powered Bug Hunting, and Self-Improving Claude Workflows

**Guests:** none (regular hosts only)
**Hosts:** Justin Gardner (Rhynorater), Joseph Thacker
**One-line summary:** A news-roundup-style episode covering a Protobuf decoding workflow for
Google-style targets, a self-improving CLAUDE.md pattern, a client-side permission/CSRF chain, and
a broader discussion of how much of day-to-day hunting is now AI-delegated.

## TL;DR

- Justin's Protobuf/ProtoJSON decoding workflow for Google targets: a Kaido convert-workflow that
  decodes base64 binary Protobuf inline, plus using Claude to reverse-engineer the wire format
  (including recomputing checksums) well enough to inject into specific fields.
- The most concrete, reusable idea: append to CLAUDE.md an "Applied Learning" section every time
  the agent needs correcting, retried a failing approach, or had to be re-explained something —
  described explicitly as "a self-improving loop that will then level up your own local Claude."
- A whack-a-mole false-positive-calibration workflow: feed false-positive/mis-rated findings back
  into the prompt/config as they're discovered, incrementally tightening triage accuracy.
- A real bug chain: login-CSRF + persistent mic-recording permission + CSRF on the "start
  recording" endpoint = account-agnostic microphone spying, because the permission was
  domain-locked rather than account-locked.
- Context on why AI-assisted hunting suddenly works well: CyberBench pass rate reportedly jumped
  from ~30% to ~69% on the most recent model generation — framed as the reason hackbots no longer
  need heavy specialized wrappers/prompts to be effective.

## Key techniques

- **Protobuf/ProtoJSON manual decoding for Google targets** — base64-encoded binary Protobuf
  parameters are common and often dismissed as "signed" or untouchable; in practice they're
  unsigned data-only fields (no key names), decodable by hand: first 3 bits = wire type, next 4
  bits = field number, last bit = continuation flag, followed by the actual value. Protobuf often
  nests inside Protobuf. A browser-proxy convert-workflow that decodes these inline, plus handing
  the decoded buffer to Claude to write a one-off Python parser/re-encoder (including checksum
  recomputation where Google's format includes one), turns an opaque blob into an injectable,
  structured target.
- **Self-improving CLAUDE.md via an "Applied Learning" section** — a standing rule: any time the
  agent is corrected, has to retry a failing command/approach, or needs something re-explained,
  that correction gets appended to a dedicated section of CLAUDE.md. The next session reads it and
  doesn't repeat the mistake (concrete example given: learning once where session files live,
  instead of re-discovering it via 20 failed bash commands every time).
- **False-positive whack-a-mole calibration** — after each run, review what the agent flagged as
  critical/high that was actually a false positive or over-rated, and fold that specific correction
  back into the prompt/config ("when you found this, it was a false positive because of X; this
  class of finding is actually a low, not a critical").
- **Account-locked vs. domain-locked browser permissions as a bug class** — browser permission
  grants (mic, camera, screen-share) are scoped to the *domain*, not the authenticated *account* on
  that domain. Chaining login-CSRF (force victim into attacker's own account) with a CSRF on a
  "start recording" endpoint lets an attacker capture audio/video into their own account using a
  permission the victim granted to the domain generically, not to the victim's session specifically.
- **iframe permission delegation via the `allow` attribute** — a third-party iframe embedded in a
  trusted top-level page can inherit sensitive permissions (mic, camera, screen capture) if the
  parent page's `allow` attribute delegates them; worth checking delegation scope whenever a page
  embeds a cross-origin iframe you don't control.
- **Traffic-history "memory" for per-target context** — highlighting free-form text in a proxy
  (e.g. a known user ID) and hotkey-storing it so it's automatically included in every subsequent
  AI query against that target, rather than manually re-pasting context each time.

## Tools & resources

- **bbScope** (by "Sweetly") — open-source scope-dumping tool; recently upgraded to use HackerOne's
  official API key auth (no more pasting session cookies) and to support dumping your own past
  reports, not just program scope.
- **Kaido Skills for Claude Code/Codex** (`github.com/kaido/skills`) — an official Kaido-maintained
  skill that lets Claude Code/Codex drive the Kaido proxy directly (add findings, edit requests,
  search HTTP history, manage match/replace rules). Cannot yet edit/create Kaido *workflows*
  (e.g. the Protobuf convert-workflow above) — flagged by the hosts as a near-term gap they intend
  to close.
- **Claude.ai privacy settings** (`claude.ai/settings/data-privacy-controls`) — disabling
  training-data use per workspace/sub-account; mentioned as a baseline step before importing your
  own disclosed reports into a Claude session.

## Notable claims & lessons

- According to Joseph: CyberBench pass rates reportedly went from ~30% to ~69% between model
  generations (Claude Opus/Sonnet 4.6 generation vs. prior), which he gives as the reason AI-
  assisted hunting stopped requiring heavy specialized wrappers and "internal monologue of
  experts" scaffolding to be effective. This is a specific, checkable benchmark claim — verify
  against the actual CyberBench leaderboard before treating it as settled fact in any write-up.
- Both hosts explicitly turned off Claude's training-data-use setting before discussing importing
  their own disclosed HackerOne reports into Claude sessions; this is presented as the baseline
  privacy precaution, not a solved legal question — there's visible disagreement among hunters
  (Tommy DeVoss pushing back on X) about whether sharing your own reports with a third-party model
  provider is permitted at all under program terms.
- Joseph, on the current limiting factor for a less-experienced hacker running an AI hackbot: not
  model capability, but knowing what's a false positive — "the real difference between that and
  what I'm doing... is that we do know."
- Anecdote: "BuzzFactor" found a high ($8K-paying program) using an AI-assisted workflow despite
  being a stated skeptic, framed by Joseph as the typical conversion story for AI-hesitant hunters
  once it actually pays out.

## Agent & automation ideas

### IDEA-165-01: Self-improving "Applied Learning" section, triggered by friction
- **category:** pipeline
- **targets:** engagement-memory system / CLAUDE.md-equivalent for our pipeline
- **why:** a concrete, low-effort trigger rule for writing to persistent memory: append whenever
  the agent is corrected, retries a failed approach, or needs re-explaining — not a vague "learn
  over time" instruction. Our [[engagement-memory-system]] already exists; this is worth checking
  against specifically for whether it has this exact friction-triggered write rule or something
  looser.
- **evidence:** ~34:02–35:41.
- **effort:** small.
- **confidence:** high.

### IDEA-165-02: False-positive whack-a-mole calibration loop
- **category:** pipeline
- **targets:** verifier.md, severity-calibration skill
- **why:** describes incrementally folding specific false-positive/mis-rated-finding corrections
  back into the running prompt/config — check this against our existing RAG-based false-positive
  learning ([[ai-hunting-repo-audit]] notes we already have real corpus data here) to see if it's
  the same mechanism described from a different angle, or a complementary one (per-session prompt
  patching vs. persistent RAG lookup).
- **evidence:** ~32:00–33:21.
- **effort:** n/a — likely a comparison/validation item, not new work.
- **confidence:** medium.

### IDEA-165-03: Protobuf/ProtoJSON decoding as a dedicated skill
- **category:** skill
- **why:** specific, technical, and narrow enough to be a clean skill: wire-type/field-number/
  continuation-bit structure, nested-Protobuf handling, checksum-recomputation heuristics, and the
  instruction to hand a decoded buffer to the model for one-off parser/re-encoder generation. This
  looks like a genuinely missing skill (narrow protocol-encoding topic, not currently covered by
  the `protocols/` collection per [[skill-library-architecture]] as of 2026-10-23) — needs
  confirming absent before building, per standing no-dupes rule.
- **evidence:** ~14:11–20:19 (the full technical walkthrough).
- **effort:** medium.
- **confidence:** high.

### IDEA-165-04: Account-locked vs. domain-locked permission bug class
- **category:** skill
- **why:** a specific, reusable bug pattern (login-CSRF + CSRF on a permission-gated endpoint +
  domain-scoped-not-account-scoped browser permission grant) distinct from generic CSRF or generic
  permissions-API coverage. Check whether this compound pattern is explicit in existing client-
  side/CSRF skills or only the individual primitives are covered separately.
- **evidence:** ~24:40–26:41.
- **effort:** small (skill content only).
- **confidence:** high.

### IDEA-165-05: iframe permission delegation via the `allow` attribute
- **category:** skill
- **why:** a specific, checkable standing instruction — "whenever you see a controllable iframe,
  check its `allow` attribute for delegated sensitive permissions" — this is small enough to be a
  checklist line inside an existing client-side/iframe skill rather than its own skill; flag for
  gap-analysis to confirm it's not already there.
- **evidence:** ~27:00–27:39.
- **effort:** small.
- **confidence:** high.

### IDEA-165-06: Ingest disclosed reports to auto-build reusable skills
- **category:** pipeline
- **why:** same idea as IDEA-102-10 from a different episode and different speakers (Joseph citing
  his own and "Corbin's" practice of dumping past reports and telling Claude to "build yourself a
  set of skills to implement the techniques referenced in this report"). Flag explicitly as a
  dedup/merge candidate — two independent sources converging on the same idea is a stronger signal
  than either alone.
- **evidence:** ~07:58–08:15.
- **effort:** medium.
- **confidence:** high.

### IDEA-165-07: Traffic-history "memory" hotkey for per-target context
- **category:** tooling
- **why:** describes a lightweight UX pattern (highlight text, hotkey, auto-injected into every
  subsequent query against that target) for carrying known IDs/params forward without re-pasting
  context — smaller and more general version of IDEA-102-09's traffic-learned parameter detection.
- **evidence:** ~37:34–38:58.
- **effort:** small.
- **confidence:** medium — this is describing a UI feature of a specific third-party tool (Shift),
  not something independently verified; treat as a pattern to replicate, not a tool to depend on.

## Follow-ups

- Kaido's official skill repo at `github.com/kaido/skills` — worth checking periodically for when
  workflow-editing support lands, since that would let an agent create/modify Protobuf-style
  convert-workflows on the fly rather than needing one hand-built in advance.
- The CyberBench 30%→69% claim above — verify against the actual published benchmark before
  reusing as a cited figure anywhere.

## Related episodes

- Episode 102 — "Building Web Hacking Micro Agents with Jason Haddix" — independently covers the
  same "ingest disclosed reports to build skills" idea (IDEA-165-06 / IDEA-102-10) and a related
  automated-regression-testing-bot concept that pairs naturally with this episode's false-positive
  calibration loop (IDEA-165-02).
