---
episode: 165
title: "Protobuf Hacking, AI-Powered Bug Hunting, and Self-Improving Claude Workflows"
tags: [ai-agents, web-client-side, tooling-automation, auth-identity]
primary_tag: ai-agents
source: "https://www.criticalthinkingpodcast.io/episode-165-protobuf-hacking-ai-powered-bug-hunting-and-self-improving-claude-workflows/"
transcript: "../transcripts/165-protobuf-hacking-ai-powered-bug-hunting-and-self-improving-claude-workflows.txt"
transcript_quality: official
---

# Episode 165 — Protobuf Hacking, AI-Powered Bug Hunting, and Self-Improving Claude Workflows

**Guests:** None
**Hosts:** Rhynorater, rez0
**One-line summary:** Justin and Joseph cover decoding and attacking Base64 protobuf, AI-driven bug hunting, a client-side microphone-permission chain, and a self-improving Claude Code workflow.

## TL;DR

- Base64 protobuf in Google-style APIs is often decodable and not necessarily signed. Decode nested fields, map field numbers to UI values, modify them, re-encode, and inject — Claude is unusually good at writing the parser and spotting checksums.
- BBscope's update improves scope export with HackerOne API keys, and a new pull request adds report export. Feeding your own reports to Claude to build skills is powerful, but it raises IP and privacy questions.
- AI bug hunting is shifting the human role toward triage and validation; the next bottleneck is telling a real finding from a false positive.
- A login-CSRF plus microphone-recording CSRF chain can turn a domain-scoped permission into surveillance of a victim's audio; relatedly, check iframe `allow` delegation for the same permissions.
- Joseph's self-improving CLAUDE.md loop captures corrections into an "applied learning" section; a Discord bot wrapping Claude sessions gives remote control from a phone.

## Key techniques

- **Protobuf decoding and injection** — Many API parameters are Base64-encoded binary protobuf, sometimes nested. Decode to see field numbers and values (key names are usually absent), map field numbers to strings and IDs visible in the UI, then modify and re-encode. The wire format uses 3 bits for wire type, 4 bits for field number, and 1 bit as a continuation flag; payloads can be length-delimited strings, varints, or fixed-size integers. Watch for checksums — Claude can often infer them and recalculate.
- **Caido protobuf convert workflow** — Justin built a Caido convert workflow that decodes Base64 protobuf inline, so nested structures can be inspected without leaving the proxy.
- **BBscope report export** — Dump scope for public and private programs using a HackerOne API key; a new pull request adds report export. Feed those reports to Claude to generate skills that implement the techniques.
- **AI bug hunting on ignored surfaces** — Claude Code and XSS Doctor found a high or critical finding through survey and feedback endpoints that hunters normally skip, mirroring the TRACE-header class of findings.
- **Login CSRF plus microphone recording** — Log the victim into the attacker's account with login CSRF, then CSRF the "start recording" endpoint. If the browser has a persistent microphone permission for the site, no further interaction is needed, and the recording is uploaded to the attacker's account. Domain-locked permissions arguably need to be account-locked.
- **Iframe permission delegation** — Check the `allow` attribute on iframes for microphone, camera, and screen permissions. A third-party iframe inside a trusted top-level page may inherit and abuse those permissions.
- **Self-improving CLAUDE.md** — Add a standing instruction: whenever the user gets frustrated, has to re-explain something, or watches Claude fail a command repeatedly, Claude should append the lesson to an "applied learning" section. Example: Claude learned where session files live, so the next search succeeded on the first try.
- **Discord-based Claude session control** — A Discord bot creates a thread per session and renders tool calls, emoji todo lists, and diffs using Discord's `diff` code block (green additions, red removals). The top of the thread carries a `--resume` command, and a slash command resumes the session. It accepts audio attachments and can be driven from a phone or VPS.
- **False-positive feedback loop** — When the agent reports a false positive or misrates severity, feed the correction back into the prompt. Repeated whack-a-mole improves accuracy over the next week.
- **Markdown-to-slides and labs** — Write an outline in a `.md` file, give it to Claude Code, and generate a static slide site with interactive labs embedded; deploy it to a VPS.

## Tools & resources

- **BBscope** — Sweetly/Sweet Lie's scope and report export tool; supports HackerOne API keys and a new report-export pull request.
- **OriginalSickSec's MCP-style HackerOne client** — an alternative for listing and searching your reports from an MCP client.
- **Caido** (transcribed as "Kaido") and **Caido skills for Claude Code/Codex** — `github.com/caido/skills` (transcribed as "kaido/skills"). The skill can add findings, edit requests, search HTTP history, and add match/replace rules; workflow editing and sitemap access are still pending.
- **Claude Code / Claude Opus 4.6 / Sonnet 4.6 / Haiku** — the models used for bug hunting, protobuf parsing, report ingestion, and remote sessions.
- **XSS Doctor** — the AI tool Joseph used on survey endpoints.
- **Discord bot** — Joseph's custom wrapper for remote Claude sessions, with diff rendering, todo tracking, and `--resume`.
- **Claude RC** — remote-control feature; Justin found the interface visually good but functionally limited, and it does not support `dangerously skip permissions`, which is why Joseph built his own Discord bot.
- **Matt Brown's YouTube channel** and his X handle **@nmatt0**, plus his courses *Digital Signal Analysis for Hardware Hackers* and *Beginner's Guide to IoT and Hardware Hacking*.
- **Space Raccoon's coverage-map approach** — listing every endpoint and bug class in a Google Sheet and marking tested items off; the inspiration for a sitemap-based coverage map.
- **ThreatLocker** — sponsor segment on ring-fencing via mini-filter drivers, Windows Filtering Platform, and ExVersion kernel notification routines.
- **CyberBench** — the benchmark cited for the jump in model hacking capability.

## Notable claims & lessons

- Protobuf is often not signed; fear of it is misplaced. Decoding it unlocks bugs that were previously hard to test.
- Claude can parse and re-encode protobuf, and infer checksums with a high hit rate. Joseph notes that was not true a year earlier.
- Joseph argues that importing your own reports into Claude or Codex is acceptable when the provider's training setting is disabled. Tommy's counterargument is that researchers lack the right to share reports with third parties; Alex's position is that researchers retain IP in their reports.
- Justin's view: unless you hold something like a Pegasus exploit or a high-value Android chain, the risk of a provider inspecting your bug bounty reports is negligible — but still turn off data training in settings.
- AI found bugs in survey and feedback endpoints, and in TRACE-style headers, that experienced hunters habitually ignore.
- Joseph's role over the previous six weeks shifted toward triage and validation. He says AI expands his architectural understanding of large targets such as Google, even if it may degrade some manual skills over time.
- AI can also waste time: Justin spent two hours trying to get Claude to perform a CSS-injection exfiltration he could have done manually.
- CyberBench's pass rate rose from 30% to 69%; Douglas Day reportedly found five figures' worth of bugs mostly with Haiku in a couple of weeks.
- Joseph says "anyone can do it" is true only for people who already understand APIs, servers, and hacking. The next limiting factor is false-positive judgment.
- BuzzFactor found an $8K high with AI after being skeptical; Joseph notes one such bug can pay for years of model usage.

## Agent & automation ideas

### IDEA-165-01: Protobuf decode/re-encode skill
- **category:** skill
- **targets:** new `protobuf-hacking` skill; consumed by `hunter.md`
- **why:** Base64 protobuf hides IDs, strings, and nested structures that are hard to test manually. A skill that decodes fields, maps field numbers to UI values, modifies them, re-encodes, and recalculates checksums unlocks a whole bug class.
- **evidence:** 00:14:11–00:20:19 (decode workflow, wire format, Claude-generated parser, checksum inference)
- **effort:** medium
- **confidence:** high

### IDEA-165-02: Caido coverage-map integration
- **category:** tooling
- **targets:** `my-tools/` or a Caido plugin
- **why:** A sitemap coverage tree that marks endpoints red until tested and green after testing would make thoroughness visible and prevent skipped endpoints.
- **evidence:** 00:19:01–00:21:30 (Caido skill capabilities, sitemap tree, Space Raccoon's coverage map)
- **effort:** large
- **confidence:** medium

### IDEA-165-03: Applied-learning memory loop
- **category:** agent
- **targets:** agent system prompts / `AGENTS.md`
- **why:** Repeated failures and user corrections are currently lost between sessions. A standing instruction to append them to an "applied learning" section makes the agent improve and saves tokens.
- **evidence:** 00:33:21–00:35:41 (CLAUDE.md instruction, session-search example)
- **effort:** small
- **confidence:** high

### IDEA-165-04: Remote Claude session control via chat bot
- **category:** ops
- **targets:** harness/ops tooling
- **why:** A chat bot that streams tool calls, todo lists, and diffs, and supports `--resume`, lets a researcher validate findings or check logs from a phone without the Claude mobile interface.
- **evidence:** 00:35:46–00:41:37 (Discord bot, diff blocks, resume, audio/attachments)
- **effort:** medium
- **confidence:** high

### IDEA-165-05: Report-to-skill generation pipeline
- **category:** pipeline
- **targets:** `notes/` and skills pipeline
- **why:** Export your reports, feed them to Claude, and generate skills that implement the techniques they reference. This turns past work into reusable agent capability.
- **evidence:** 00:06:25–00:09:38 (BBscope report export; "build yourself a set of skills")
- **effort:** medium
- **confidence:** medium

### IDEA-165-06: False-positive feedback loop
- **category:** pipeline
- **targets:** `verifier.md` / `hunter.md`
- **why:** False positives and severity misratings are the main quality problem in AI bug hunting; feeding corrections back into the prompt improves the next run.
- **evidence:** 00:31:26–00:33:21 (whack-a-mole with false positives and ratings)
- **effort:** small
- **confidence:** high

### IDEA-165-07: Permission-delegation and login-CSRF audit skill
- **category:** skill
- **targets:** new `browser-permissions` skill; related to `browser-security`
- **why:** Login CSRF plus persistent microphone permission can turn a site into a surveillance tool, and iframe `allow` delegation can leak microphone, camera, or screen access to a third-party frame.
- **evidence:** 00:24:40–00:27:30 (login CSRF, recording endpoint, account-locked permissions, iframe `allow`)
- **effort:** small
- **confidence:** high

### IDEA-165-08: AI triage and validation agent
- **category:** agent
- **targets:** `verifier.md`
- **why:** As AI finds more candidates, human effort shifts to validation. An agent that reproduces, classifies false positives, and rates severity would absorb that bottleneck.
- **evidence:** 00:22:58–00:24:36 (job becoming triage/validation), 00:31:26–00:33:21 (false-positive judgment)
- **effort:** medium
- **confidence:** high

### IDEA-165-09: Markdown-to-slides and labs generator
- **category:** tooling
- **targets:** none (research/communication tooling)
- **why:** Hackers hate slide prep; an outline in Markdown can become a static slide site with interactive labs, deployed to a VPS.
- **evidence:** 00:03:27–00:04:34 (outline to website, labs embedded, static hosting)
- **effort:** small
- **confidence:** high

## Follow-ups

- Try BBscope's report export and review provider data-training settings (Claude.ai → Settings → Privacy).
- Build a protobuf decode/encode test against a Google-style API; compare a Claude-generated parser with a manual one.
- Audit iframe `allow` attributes and login-CSRF chains on applications with microphone, camera, or screen permissions.
- Add the "applied learning" instruction to CLAUDE.md and measure whether repeated searches and command failures drop.
- Evaluate a chat-bot control channel for remote Claude sessions.
- Open question: where is the line on uploading reports to model providers, and does it change with private programs?
- Open question: how much of AI's output is false positives, and how should that be measured before trusting a skill?

## Related episodes

- Episode 102 — companion episode on micro agents, prompt engineering, and web-hacking automation.
