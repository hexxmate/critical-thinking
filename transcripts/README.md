# Transcripts

Plain-text transcripts for **193 of the 199** episodes of the Critical Thinking - Bug Bounty
Podcast.

## Coverage

| Source | Episodes | Quality |
|---|---:|---|
| Official podcast transcripts | 166 | Timestamps and speaker labels |
| YouTube auto-generated captions | 27 | No punctuation or speaker labels; may contain errors |
| No public transcript | 6 (episodes 1–6) | Only audio exists |

The 27 YouTube-derived episodes are: 7–19, 25, 27, 29, 42, 52, 84, 86, 87, 89, 99, 100, 105, 113,
and 115. Each file's header states its source.

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
| `type` | `official-site` or `youtube-captions` |
| `chars` | Character count of the transcript body |

Regenerate the manifest whenever transcripts are added or replaced.

## Notes

- Episodes 1–6 are absent because the podcast has no published transcript for them and they were
  not uploaded to YouTube. Their show-note links are still included in [`../links/`](../links/).
- YouTube captions are useful for search but should not be quoted verbatim without checking the
  audio.
- Transcript content belongs to the podcast. See [`../NOTICE.md`](../NOTICE.md).
- The episode index with links to each file is in
  [`../docs/EPISODE-INDEX.md`](../docs/EPISODE-INDEX.md).
