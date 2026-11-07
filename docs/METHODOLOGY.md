# Methodology

This document explains how the archive was built, so contributors can reproduce it and understand
its limitations.

## Sources

| Source | Used for |
|---|---|
| `criticalthinkingpodcast.io` episode pages | Official transcripts, episode metadata |
| `/episode-slug/transcript.txt` endpoint | Plain-text official transcripts |
| Podcast RSS feed (`feeds.cohostpodcasting.com`) | Show notes, descriptions, episode audio URLs |
| YouTube channel `@criticalthinkingpodcast` | Fallback captions for episodes without site transcripts |

## Process

1. **Enumerate episodes.** Parsed the nine paginated listing pages, deduplicated episode cards, and
   built an index of all 199 episodes. Two duplicate cards for episodes 158 and 159 were collapsed.
2. **Fetch transcripts.** For each episode, extracted the transcript block from the page HTML with
   an HTML parser, preserving paragraph breaks. For pages where the block was empty, fell back to
   the plain-text `transcript.txt` endpoint.
3. **Fill gaps.** For 27 episodes with no site transcript, retrieved English captions from the
   show's YouTube uploads. These are labelled in each file header.
4. **Extract links.** Parsed every `<item>` in the RSS feed, extracted URLs from the
   `description` and `content:encoded` fields, and merged in inline URLs found in transcripts.
5. **Normalize and categorize.** Removed tracking parameters, fragments, podcast-platform
   boilerplate, and asset URLs. Deduplicated and split into nine categories.
6. **Validate.** Checked file counts, minimum lengths, duplicate URLs, and malformed entries.

## Quality notes

- **Official transcripts** (166) include timestamps and speaker labels and are the highest-quality
  source.
- **Episodes 1–6** have no transcript anywhere public. Their show notes are still reflected in the
  link archive.
- **Links** reflect the show notes at extraction time. Expect link rot.
- **Transcript headers** are added by this archive: episode number, title, and source URL. The body
  is the original transcript.

## Amendment — YouTube-caption transcripts removed (2026-11-07)

The archive originally also included 27 auto-generated YouTube caption transcripts (episodes
7–19, 25, 27, 29, 42, 52, 84, 86, 87, 89, 99, 100, 105, 113, 115) as a fallback for episodes with
no official site transcript. They were removed ahead of the idea-extraction phase (see
[`ROADMAP.md`](ROADMAP.md) Phase 1): no punctuation, no speaker labels, and occasional
mis-hearings made them too low-confidence to feed a structured extraction pipeline reliably, and
re-adding them later is cheap (they're just a re-fetch from YouTube) if a future pass wants them
back at an explicit lower-confidence tier. Their show-note links remain in
[`../links/`](../links/); they are now treated the same as episodes 1–6 — catalogued but with no
local transcript.

## Reproducibility

The archive can be rebuilt from the public sources above. If you change the extraction logic,
document the change in a pull request and re-run the validation checks:

- 166 transcript files, numbered `NNN-title.txt` (27 YouTube-caption-only files removed 2026-11-07).
- No file shorter than 5,000 characters.
- One URL per line in `links/`, no duplicates.
- `transcripts/_manifest.tsv` matches the files on disk.

## Privacy and ethics

Only public content is archived. No private, paywalled, or authenticated material is included.
Credentials, tokens, and personal data are not collected. Linked content remains the property of
its authors.
