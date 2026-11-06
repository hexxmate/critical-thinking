# Taxonomy

## Why tags instead of folders

Many episodes cover several unrelated subjects: a single episode can move from a client-side bug to
an AI workflow to a career question. Moving episodes into topic folders would force a false choice
and break the chronological archive.

Instead:

- Files stay in `notes/` and `transcripts/`, named by episode number.
- Every episode receives **multiple tags** in its frontmatter.
- Topic indexes are **generated** from those tags.

This keeps one source of truth and makes it trivial to list every episode about, say, Android
without duplicating files.

## Proposed tags

Start with a small, controlled vocabulary. Add a tag only when at least two episodes need it.

| Tag | Covers |
|---|---|
| `ai-agents` | LLMs, AI-assisted hacking, hackbots, local models, prompt injection, agent workflows |
| `web-client-side` | XSS, CSP, DOM clobbering, postMessage, CORS, cache deception, prototype pollution, browser internals |
| `web-server-side` | SSRF, SQLi, SSTI, deserialization, path traversal, file upload, request smuggling |
| `auth-identity` | OAuth, OIDC, SAML, JWT, MFA, SSO, session management, account takeover |
| `mobile` | Android, iOS, mobile app testing, Frida, deep links, mobile backends |
| `iot-hardware` | Embedded devices, firmware, radio, hardware hacking, IoT attack surfaces |
| `cloud-infra` | AWS, GCP, Azure, Kubernetes, CI/CD, supply chain, infrastructure |
| `tooling-automation` | Burp extensions, recon tooling, scripts, proxies, automation, developer workflows |
| `bug-bounty-career` | Program strategy, triage, motivation, full-time hunting, report writing, income |
| `research-writeups` | Deep dives, CVE analysis, novel technique research, case studies |
| `community-events` | Live hacking events, conferences, interviews, community topics |
| `web3` | Smart contracts, blockchain, decentralized applications |

If a subject grows beyond a handful of episodes — for example `android` or `llm-security` — split it
out of its parent tag and update the index.

## Frontmatter format

Every summary in `notes/` starts with:

```yaml
---
episode: 123
title: "Hacking AI Series: Vulnus ex Machina - Part 2"
tags: [ai-agents, web-server-side, tooling-automation]
primary_tag: ai-agents
---
```

- `tags` — 1 to 5 tags, ordered by relevance.
- `primary_tag` — the single best fit, used for default grouping and sorting.
- Tag values must match a slug in the table above.

## Generated indexes

Once episodes are tagged, a script can produce:

```text
indexes/
├── ai-agents.md
├── web-client-side.md
├── web-server-side.md
├── auth-identity.md
├── mobile.md
├── iot-hardware.md
├── cloud-infra.md
├── tooling-automation.md
├── bug-bounty-career.md
├── research-writeups.md
├── community-events.md
└── web3.md
```

Each index lists the episode number, title, one-line takeaway, and a link to the summary. Indexes
are generated artifacts; do not edit them by hand.

## Adding a tag

1. Open an issue describing the tag and at least two episodes that need it.
2. Add the tag to this table with a one-line description.
3. Apply it to the relevant summaries.
4. Regenerate the indexes.

Keep the vocabulary intentionally small. A tag that applies to one episode is a note, not a
category.
