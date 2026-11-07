---
episode: 102
title: "Building Web Hacking Micro Agents with Jason Haddix"
tags: [ai-agents, tooling-automation, web-server-side, bug-bounty-career]
primary_tag: ai-agents
source: "https://www.criticalthinkingpodcast.io/episode-102-building-web-hacking-micro-agents-with-jason-haddix/"
transcript: "../transcripts/102-building-web-hacking-micro-agents-with-jason-haddix.txt"
transcript_quality: official
---

# Episode 102 — Building Web Hacking Micro Agents with Jason Haddix

**Guests:** Jason Haddix
**Hosts:** Rhynorater
**One-line summary:** Jason Haddix explains how he builds narrow, prompt-engineered "micro agents" for recon and web hacking, and brainstorms concrete bots for fuzzing, WAF bypass, regression testing, and report writing.

## TL;DR

- A micro agent works because its scope is tiny: one task, one small input, one small output. Jason argues the quality comes from prompt engineering at every step, not from an elaborate agent framework.
- His Acquisition Finder surfaced small Tesla acquisitions that Crunchbase missed and led to bugs on a Tesla-owned insurance carrier; his Subdomain Doctor predicts likely subdomains from a known list.
- The most promising web-hacking bots are scoped fuzzers — open redirect, WAF bypass, path traversal — each with a tiny input, a tiny output, a unique request key, and a response parser.
- A regression-testing bot can watch a report's status, generate bypasses based on how developers usually fix bugs, and notify the researcher when a fix is ready to test.
- Jason routes work across models (OpenAI for analysis, Claude for code and attack strings, Perplexity for search) and spends roughly $400–500 per month on tokens.

## Key techniques

- **Decompose the work into micro agents** — Instead of asking one model to "hack this site", parse the application, identify inputs, classify likely vulnerability classes, route each class to a specialist agent, execute the HTTP requests, and have a response parser feed results back to a human. Jason describes three workflows: parsing, fuzzing, and manual-testing handoff.
- **Acquisition discovery** — Ask an LLM for a company's acquisitions and subsidiaries, then verify each lead independently. It surfaced two Tesla acquisitions absent from Crunchbase, including an insurance carrier that led to bounty-eligible bugs.
- **Subdomain extrapolation** — Give a model the known subdomain list and ask it to predict additional likely names, then validate by resolution.
- **Tiny-scope HTTP fuzzing** — Provide one request, one parameter, one restriction, and the one response fragment that matters (for example, the `Location` header). Give the agent string-replace and HTTP-send tools and let it iterate. Tag every generated request with a unique key or hash so the winning payload can be mapped back.
- **WAF-bypass probing** — Reuse the backslash-powered-scanner idea: send a special character, observe how the application reacts, send the escaped version, and compare. Tell the agent exactly what the WAF response looks like (status code, text, page length, even colours or images) and give it a regex or parser so it does not re-read the whole body on every attempt.
- **Regression testing** — Feed the bot the report plus the developer's likely fix pattern (a regex denylist or a WAF rule) and ask for bypasses. In the episode's JetBrains Markdown-XSS example, the bot produced two bypasses: splitting the payload across three lines with null characters in the separator, and Base64-encoding the payload.
- **Obfuscation-bot report writing** — A local model redacts the domain, cookies, and other sensitive data; a cloud model writes the report; the local model restores the real values afterwards. A hosted Azure OpenAI instance under an existing contract is the alternative.
- **CVSS impact writing** — Write the impact/CVSS section explicitly. A common error is treating a free account signup as "privileged access", which lowers the score.
- **Prompt-engineering tricks** — CTF framing, a pre-seeded user prompt, urgency to force search or tool use, technical keyword seeding, and putting world-class research documents in context to narrow the model's output space.

## Tools & resources

- **Acquisition Finder GPT** and **Subdomain Doctor** — Jason's custom GPTs for acquisition discovery and subdomain prediction.
- **ChatGPT / OpenAI API**, **Claude 3.5 Sonnet**, **Perplexity**, **Gemini** (Jason avoids it), and local **Llama** models.
- **Caido** (transcribed as "Kaido") and **Shift** — the AI plugin for Caido, in closed beta at shiftwaitlist.com. Shift exposes Replay, Automate, match-and-replace, and HTTPQL actions to a model.
- **Burp Suite** and **Puppeteer/Playwright** — Jason notes that DEF CON AICC teams tend to instrument web hacking with Puppeteer/Playwright rather than an interception proxy.
- **Hunt** — Jason's tool for statistically probable parameter names by vulnerability type.
- **3Blue1Brown's transformer/attention video** — Jason credits it with helping him reason about prompt engineering.
- **Jason's talks** — "Red, Blue, Purple AI" and "The Dark Side of Bug Bounty".
- **Azure-hosted OpenAI** — a private deployment option for sensitive report data.
- **JetBrains Markdown-XSS CVE** (transcribed as "CBE") — the case study used to demonstrate the regression-testing bot.

## Notable claims & lessons

- "It is all prompt engineering that makes these micro bots good. All of it. It's all prompt engineering in every single step." — Jason Haddix.
- The Acquisition Finder found two Tesla acquisitions that were not on Crunchbase; bugs on the acquired insurance carrier fed into the Tesla bounty.
- Jason spends roughly $400–500 a month on tokens across his AI tools; before his newest OpenAI subscription it was more than $300.
- Model routing: OpenAI for contextual analysis, Claude 3.5 Sonnet for code and attack strings, Perplexity for search. Gemini was avoided after the "Elmer's glue on pizza" training-data incident.
- Local models were not good enough for report writing in Justin's testing; Jason says the large SaaS models have always performed best for him.
- Jason says it is "100% happening" that bug bounty platforms train on submitted attack traffic and build attack bots, threat feeds, and auto-triage, and argues researchers should either get a revenue share or the practice should stop.
- Triage bias is real: a mentee's report can be rejected while the same finding, submitted by a well-known researcher, is accepted.
- Most researchers under-rate the privilege required in CVSS because they count a free account signup as privileged access.

## Agent & automation ideas

### IDEA-102-01: Scoped HTTP fuzzing micro-agent
- **category:** skill
- **targets:** new `web-fuzzing` skill; consumed by `hunter.md`
- **why:** General "hack this site" prompts produce poor output; a narrow agent with one request, one parameter, one restriction, and one response field can iterate bypasses reliably and cheaply.
- **evidence:** 10:57–11:25 (small input/output), 13:30–14:24 (string-replace and HTTP-send tools), 20:22–21:29 (unique key per request)
- **effort:** medium
- **confidence:** high

### IDEA-102-02: Acquisition Finder recon agent
- **category:** tooling
- **targets:** `recon.md` or a new acquisition-lookup tool in `my-tools/`
- **why:** LLMs surface small acquisitions and subsidiaries that Crunchbase misses, expanding scope and producing bounty-eligible assets.
- **evidence:** 01:54–03:51 (Tesla acquisitions not in Crunchbase; bugs on the insurance carrier)
- **effort:** small
- **confidence:** high

### IDEA-102-03: Subdomain prediction agent
- **category:** tooling
- **targets:** `subleech`
- **why:** Predict likely subdomain names from a known list, then resolve and probe only the plausible candidates.
- **evidence:** 09:59–10:28 (probabilistic subdomain extrapolation)
- **effort:** medium
- **confidence:** high

### IDEA-102-04: WAF-bypass triage agent
- **category:** skill
- **targets:** new `waf-bypass` skill; consumed by `hunter.md`
- **why:** Char-by-char WAF probing is slow and repetitive; an agent can send canaries, classify the WAF response, and iterate encodings/escapes.
- **evidence:** 17:58–19:26 (backslash-powered scanner, response signatures, regex/parser)
- **effort:** medium
- **confidence:** high

### IDEA-102-05: Regression-testing bot
- **category:** agent
- **targets:** `hunter.md` and `verifier.md`
- **why:** When a report is marked resolved, the bot can generate bypasses from the developer's likely fix pattern, test them, and notify the researcher — replacing calendar reminders and manual re-testing.
- **evidence:** 22:25–26:31 (report status, developer fix patterns, JetBrains Markdown-XSS bypasses)
- **effort:** large
- **confidence:** high

### IDEA-102-06: Obfuscation-bot report pipeline
- **category:** pipeline
- **targets:** `reporter.md`
- **why:** Report data is the most sensitive traffic; a local redaction model plus a cloud writing model preserves privacy without giving up model quality.
- **evidence:** 51:11–52:32 (redact domain/cookies, cloud writes, restore placeholders)
- **effort:** medium
- **confidence:** high

### IDEA-102-07: Prior-report and app-context ingestion
- **category:** pipeline
- **targets:** `walker.md` / `orchestrator.md`
- **why:** Previous reports and human-supplied business context (for example, "this app handles telephony billing") let agents find esoteric bugs that cannot be inferred from HTML alone.
- **evidence:** 40:18–43:44 (talk to the proxy, ingest reports, RAG over pen-test reports)
- **effort:** large
- **confidence:** high

### IDEA-102-08: Parameter and identity discovery agent
- **category:** skill
- **targets:** new `parameter-discovery` skill; consumed by `hunter.md`
- **why:** Automatically identify user IDs, auth headers, and cookies from captured traffic, then build authenticated and unauthenticated requests for testing.
- **evidence:** 46:39–49:13 (Hunt-style probable parameter names, curl generation, Swagger-to-request conversion)
- **effort:** medium
- **confidence:** high

### IDEA-102-09: CVSS impact writer
- **category:** skill
- **targets:** `reporter.md`
- **why:** Impact sections are often one line and systematically under-rate privilege requirements; a dedicated writer can produce a granular, defensible CVSS justification.
- **evidence:** 57:38–58:58 (free signup is not privileged access; AI writes the impact section)
- **effort:** small
- **confidence:** high

### IDEA-102-10: Prompt-hardening pack
- **category:** pipeline
- **targets:** all agent system prompts
- **why:** CTF framing, pre-seeded user prompts, urgency to force tool use, and technical keyword seeding improved Jason's results across bots and are cheap to add.
- **evidence:** 06:37–08:24 (urgency to force search), 38:27–39:25 (CTF framing and pre-seeded prompts)
- **effort:** small
- **confidence:** high

### IDEA-102-11: Task-based model router
- **category:** ops
- **targets:** `orchestrator.md` model-selection layer
- **why:** Different models are better at different jobs; routing analysis to OpenAI, code/attack strings to Claude, and search to Perplexity improves output and controls cost.
- **evidence:** 33:09–34:05 (model benchmarks and routing choices)
- **effort:** small
- **confidence:** high

## Follow-ups

- Watch Jason's "Red, Blue, Purple AI" and "The Dark Side of Bug Bounty" talks.
- Prototype the scoped HTTP fuzzer against a lab target using Caido/Shift or Puppeteer.
- Test the Acquisition Finder and Subdomain Doctor prompts on a real scope.
- Review 3Blue1Brown's attention video to improve prompt design.
- Open question: how could the claim that platforms train on submitted traffic be verified, and what terms changes would address it?
- Open question: how do we measure a micro agent's hit rate against manual testing?

## Related episodes

- Episode 165 — companion episode on AI-powered bug hunting, protobuf, and self-improving Claude workflows.
