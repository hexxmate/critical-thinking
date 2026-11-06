# Contributing

Thanks for helping turn this archive into a useful research base. Contributions of all sizes are
welcome — a fixed link is as valuable as a full episode summary.

## Ways to contribute

| Contribution | Where | Effort |
|---|---|---|
| Fix a broken or wrong link | `links/` | Small |
| Add a missing link from an episode's show notes | `links/` | Small |
| Write an episode summary | `notes/` | Medium |
| Add or improve topic tags | `notes/`, `docs/TAXONOMY.md` | Medium |
| Extract agent/tooling ideas | `ideas/` (future) | Medium |
| Improve the taxonomy or roadmap | `docs/` | Discussion |
| Report a copyright concern | Issue | — |

If you are unsure where something belongs, open an issue first.

## Workflow

1. Fork the repository.
2. Create a branch: `git checkout -b summary/ep-123` or `fix/link-dead`.
3. Make your change.
4. Verify links and formatting locally.
5. Open a pull request describing what you changed and why.

Small fixes can go straight to a PR. For a new taxonomy or a large restructuring, open an issue
first so the direction can be agreed on.

## Episode summaries

Use [`docs/SUMMARY-TEMPLATE.md`](docs/SUMMARY-TEMPLATE.md). Name the file:

```text
notes/NNN-title.md
```

Examples:

```text
notes/123-hacking-ai-series-vulnus-ex-machina-part-2.md
notes/150-client-side-advanced-topics.md
```

Guidelines:

- **Be faithful.** Summarize what was actually said. Do not invent techniques, tools, or quotes.
- **Cite the episode.** Include the episode number and, where useful, a timestamp or transcript
  search term so others can verify.
- **Stay concise.** One page is usually enough; depth belongs in the "Techniques" section.
- **Separate facts from ideas.** If you propose an agent skill or automation inspired by the
  episode, put it under "Agent & automation ideas", not under "Techniques".
- **Tag with restraint.** Use the tags in [`docs/TAXONOMY.md`](docs/TAXONOMY.md). Add a new tag only
  when at least two episodes need it.

## Link contributions

- Keep one URL per line, in the existing Markdown format.
- Prefer canonical URLs over trackers or shorteners.
- Remove tracking parameters (`utm_*`, `?s=20`, etc.).
- If a link is dead, replace it with an archived copy (Wayback Machine) and note the change in the
  PR.
- Do not add affiliate or promotional links.

## Commit style

Short, imperative subjects:

```text
Add summary for episode 123
Fix dead link in x-profiles
Add taxonomy tag for mobile security
```

## Review

A maintainer will review for accuracy, attribution, and formatting. By contributing, you agree that
your original contribution is licensed under CC BY-NC 4.0, and you confirm that you are not
reproducing third-party material without permission.
