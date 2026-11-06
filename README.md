# Critical Thinking — Bug Bounty Podcast Archive

![Episodes](https://img.shields.io/badge/episodes-199-0A66C2)
![Transcripts](https://img.shields.io/badge/transcripts-193-2EA043)
![Links](https://img.shields.io/badge/links-1%2C255-EA1D11)
![License](https://img.shields.io/badge/license-CC%20BY--NC%204.0-lightgrey)

An open research archive of the **Critical Thinking - Bug Bounty Podcast**: full transcripts,
every link mentioned in the show notes, and a structured foundation for turning 199 episodes of
offensive-security knowledge into something agents and humans can actually use.

The long-term goal is to convert this material into **agent-ready knowledge**: episode summaries,
technique and tool indexes, and a backlog of ideas for skills, automations, and agent
architectures.

---

## Overview

[Critical Thinking - Bug Bounty Podcast](https://www.criticalthinkingpodcast.io/) is hosted by
**Rhynorater**, **rez0**, and **gr3pme**. Across 199 episodes, the show covers real bug bounty
reports, client-side and server-side research, mobile and IoT hacking, AI-assisted security,
tooling, career strategy, and interviews with many of the most active professional hunters.

This repository exists because that knowledge is difficult to search, cite, and reuse in
long-form audio. Here it is:

- **Searchable** — every transcript is a plain text file, easy to `grep` or feed to an LLM.
- **Linked** — 1,255 URLs from the show notes, split by category and ready to click.
- **Structured** — a documented roadmap and taxonomy for turning episodes into summaries,
  topic indexes, and implementable ideas.

### Who this is for

- Bug bounty hunters looking for techniques, tools, and people to follow.
- Security researchers who want a searchable corpus of real-world findings.
- AI/agent builders designing offensive-security skills, knowledge bases, and automations.
- Anyone who prefers reading over listening.

---

## What's inside

| Asset | Count | Location |
|---|---:|---|
| Episodes covered | 199 | — |
| Transcripts | 193 | [`transcripts/`](transcripts/) |
| — Official site transcripts | 166 | `transcripts/` |
| — YouTube caption transcripts | 27 | `transcripts/` |
| Extracted links | 1,255 | [`links/`](links/) |
| Episode index | 193 | [`docs/EPISODE-INDEX.md`](docs/EPISODE-INDEX.md) |
| Structured notes | coming soon | [`notes/`](notes/) |

Episodes **1–6** have no published transcript on the podcast website and were never uploaded to
the show's YouTube channel. Their show-note links are still included in the link archive.

---

## Repository structure

```text
.
├── README.md
├── CONTRIBUTING.md
├── NOTICE.md
├── LICENSE
├── transcripts/              # 193 transcript files + manifest
│   ├── README.md
│   ├── _manifest.tsv
│   └── NNN-title.txt
├── links/                    # every URL from the show notes, by category
│   ├── README.md
│   ├── x-profiles.md
│   ├── x-posts.md
│   ├── hackerone-reports.md
│   ├── hackerone-profiles-and-programs.md
│   ├── youtube.md
│   ├── github.md
│   ├── linkedin.md
│   ├── other-social.md
│   └── blogs-and-websites.md
├── docs/
│   ├── EPISODE-INDEX.md      # table of every transcript
│   ├── ROADMAP.md            # where this project is going
│   ├── TAXONOMY.md           # proposed topic tags for episodes
│   ├── METHODOLOGY.md        # how the archive was built
│   └── SUMMARY-TEMPLATE.md   # template for episode summaries
└── notes/                    # structured episode summaries (future work)
    └── README.md
```

---

## Link directory

All links were extracted from the official episode descriptions and deduplicated. Some show-note
links may have moved or expired since the episode aired.

| Category | Links | File |
|---|---:|---|
| X / Twitter profiles | 167 | [`x-profiles.md`](links/x-profiles.md) |
| X / Twitter posts | 171 | [`x-posts.md`](links/x-posts.md) |
| HackerOne reports | 18 | [`hackerone-reports.md`](links/hackerone-reports.md) |
| HackerOne profiles & programs | 29 | [`hackerone-profiles-and-programs.md`](links/hackerone-profiles-and-programs.md) |
| YouTube | 90 | [`youtube.md`](links/youtube.md) |
| GitHub / GitLab | 130 | [`github.md`](links/github.md) |
| LinkedIn | 7 | [`linkedin.md`](links/linkedin.md) |
| Other social media | 7 | [`other-social.md`](links/other-social.md) |
| Blogs & websites | 636 | [`blogs-and-websites.md`](links/blogs-and-websites.md) |

The X profile list is the fastest way to build a follow list of active hunters, researchers, and
security content creators. The HackerOne report list is a ready-made reading queue of public
disclosures discussed on the show.

---

## How to use

**Browse by episode:** start at [`docs/EPISODE-INDEX.md`](docs/EPISODE-INDEX.md).

**Search the corpus** with `ripgrep`:

```bash
# Find episodes mentioning a technique
rg -il "cache deception" transcripts/

# Find every mention of a tool
rg -in "caido|burp|ffuf|nuclei" transcripts/ | head

# Find a person
rg -il "jorian woltjer" transcripts/
```

**Feed an episode to an LLM:** each transcript is self-contained. The header contains the episode
number, title, and source URL.

**Build a follow list:** open [`links/x-profiles.md`](links/x-profiles.md).

**Find a public report:** open [`links/hackerone-reports.md`](links/hackerone-reports.md).

---

## Roadmap

| Phase | Goal | Status |
|---|---|---|
| 0 | Archive transcripts and extract links | ✅ Done |
| 1 | Structured one-page summaries for every episode | ⏳ Next |
| 2 | Multi-label topic taxonomy and per-topic indexes | 📋 Planned |
| 3 | Extract ideas: agent skills, tools, automations, architecture | 📋 Planned |
| 4 | Publish agent-ready knowledge packs | 📋 Planned |
| 5 | Keep the archive current with new episodes | 📋 Ongoing |

The full plan, including success criteria and contribution checkpoints, is in
[`docs/ROADMAP.md`](docs/ROADMAP.md).

A key design decision: episodes are **not** moved into topic folders. Most episodes cover several
subjects, so the archive stays chronological and each episode receives multiple tags. Topic indexes
are generated from those tags — see [`docs/TAXONOMY.md`](docs/TAXONOMY.md).

---

## Contributing

Contributions are welcome: episode summaries, topic tags, link fixes, taxonomy improvements, and
idea extraction. Start with [`CONTRIBUTING.md`](CONTRIBUTING.md).

If you are adding a summary, use [`docs/SUMMARY-TEMPLATE.md`](docs/SUMMARY-TEMPLATE.md) and name the
file `notes/NNN-title.md`.

---

## Attribution & disclaimer

This is an **unofficial community archive**. It is not affiliated with or endorsed by the
Critical Thinking podcast.

- Transcripts and episode content remain the property of the podcast and its hosts.
- Linked articles, reports, repositories, and profiles belong to their respective owners.
- The 27 YouTube-derived transcripts are auto-generated captions. They are readable but may
  contain transcription errors, missing punctuation, and no speaker labels. The 166 official
  transcripts include timestamps and speaker labels.
- Links were accurate in the show notes at the time of extraction; link rot is expected.

If you are a rights holder and want material removed, please open an issue and it will be handled
promptly. See [`NOTICE.md`](NOTICE.md) for details.

---

## License

Original content in this repository — the README, indexes, summaries, taxonomy, and documentation —
is licensed under [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/).

Third-party transcripts and linked content are **not** covered by that license and remain the
property of their respective owners. See [`LICENSE`](LICENSE).
