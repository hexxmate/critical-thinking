# Transcripts

Plain-text transcripts for **166 of the 199** episodes of the Critical Thinking - Bug Bounty
Podcast.

## Coverage

| Source | Episodes | Quality |
|---|---:|---|
| Official podcast transcripts | 166 | Timestamps and speaker labels |
| No public transcript | 6 (episodes 1–6) | Only audio exists |
| Removed — YouTube-caption only | 27 | See note below |

27 episodes (7–19, 25, 27, 29, 42, 52, 84, 86, 87, 89, 99, 100, 105, 113, 115) only ever had
auto-generated YouTube captions — no official transcript, no punctuation, no speaker labels.
They were removed from this archive on 2026-11-07: too low-confidence to feed the structured
idea-extraction pipeline. Their show-note links remain in [`../links/`](../links/); the episodes
themselves still exist in the podcast's own catalog.

## File format

Files are named `NNN-title.txt`, where `NNN` is the zero-padded episode number and `title` is a
lowercase slug.

Every file starts with a three-line header added by this archive:

```text
Episode 123: Episode title
Source: https://www.criticalthinkingpodcast.io/...
======================================================================
```

The rest of the file is the transcript body.

## Manifest

[`_manifest.tsv`](_manifest.tsv) is the machine-readable index:

| Column | Meaning |
|---|---|
| `num` | Episode number |
| `file` | Transcript filename |
| `title` | Episode title |
| `source` | Original URL |
| `type` | `official-site` (the only value now present; `youtube-captions` rows were removed 2026-11-07) |
| `chars` | Character count of the transcript body |

Regenerate the manifest whenever transcripts are added or replaced.

## Notes

- Episodes 1–6 are absent because the podcast has no published transcript for them and they were
  not uploaded to YouTube. Their show-note links are still included in [`../links/`](../links/).
- The 27 YouTube-caption-only episodes were removed 2026-11-07 (see Coverage above) rather than
  kept at lower confidence — see [`../docs/METHODOLOGY.md`](../docs/METHODOLOGY.md).
- Transcript content belongs to the podcast. See [`../NOTICE.md`](../NOTICE.md).
- The episode index with links to each file is in
  [`../docs/EPISODE-INDEX.md`](../docs/EPISODE-INDEX.md).
