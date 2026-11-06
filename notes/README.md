# Notes

Structured, one-page summaries of individual episodes. This is the working layer between the raw
transcripts and the future agent/idea backlog.

## Status

The folder is intentionally empty for now. Summaries are the next phase of the
[roadmap](../docs/ROADMAP.md).

## Naming

```text
NNN-title.md
```

Examples:

```text
123-hacking-ai-series-vulnus-ex-machina-part-2.md
150-client-side-advanced-topics.md
199-hardware-lhe-rehash.md
```

Use the same number and a short slug derived from the episode title. The number must match the
transcript number in [`../transcripts/`](../transcripts/).

## Template

Start from [`docs/SUMMARY-TEMPLATE.md`](../docs/SUMMARY-TEMPLATE.md). Every summary should include:

- a TL;DR,
- key techniques,
- tools and resources,
- notable claims,
- agent and automation ideas,
- follow-up links,
- related episodes.

## Tagging

Add topic tags in the frontmatter using the vocabulary in
[`docs/TAXONOMY.md`](../docs/TAXONOMY.md). Tags power the topic indexes; they do not change the
folder structure.

## Review standard

A summary is ready to merge when:

- it can be understood without listening to the episode,
- every technique and claim is traceable to the transcript,
- ideas are clearly separated from facts,
- links work,
- tags match the controlled vocabulary.
