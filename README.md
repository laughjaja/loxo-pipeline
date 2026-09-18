# loxo-pipeline

An **agent-agnostic skill** for working a [Loxo](https://loxo.co) ATS job
pipeline end to end — no manual copy-paste, no vendor lock-in.

Give any capable AI agent a Loxo job URL and it will:

1. **Pull the whole pipeline** — every candidate on the job, filtered to the
   stages you care about.
2. **Enrich each one** — profile, contact details (phone/email/LinkedIn),
   **resume text** (from the résumé tab, not the thin profile blurb), recruiter
   **activity** (who has contacted them), and **applied-vs-sourced** status.
3. **Land it as files** — one JSON + one CSV + a folder of résumé text.
4. **Build a plain interactive review page** — search, filter chips, tap-to-expand
   cards showing résumé snippet + activity + contacts.
5. **(Optional, gated) Clean up** — bulk-reject candidates, but only after
   showing you the exact list, asking for a rejection note, and testing on one.

## Why it exists

Loxo blocks large data from coming back through automation cleanly, and the old
workaround was dumping JSON into a browser tab for a human to copy-paste. This
skill removes that step: an agent reuses your **already logged-in** Loxo session
to call Loxo's own JSON endpoints and writes results straight to disk. You stay
in the loop only where it matters — logins (never automated) and any write.

## Works with any AI

The instructions in [`SKILL.md`](SKILL.md) are written for "the agent," not one
vendor. It works as:
- a **Claude / Cursor / Codex skill** (drop the folder in the agent's skills dir),
- or a plain **playbook + scripts** any agent or human can follow.

The only requirement is a transport that can reach Loxo with the user's session:
a browser-automation tool (Playwright, Puppeteer, a Chrome extension, or
`ego-browser`) or the Loxo API. Endpoints, stage IDs, and activity-type IDs are
in [`reference/loxo-endpoints.md`](reference/loxo-endpoints.md).

## Layout

```
SKILL.md                     the workflow + guardrails (read this first)
reference/loxo-endpoints.md  every endpoint, stage ID, activity-type ID
scripts/pull_pipeline.mjs    pull profiles + resumes + activity -> JSON/CSV
scripts/build_review.py      turn that JSON into the interactive review page
scripts/reject.mjs           gated bulk-reject (test-of-one, batches, reason)
```

## Quick start

1. Point your agent at this folder (or install it as a skill) and give it a Loxo
   job URL: `.../agencies/<AGENCY_ID>/jobs/<JOB_ID>/pipeline`.
2. Tell it which stages to include (default: active pipeline, no rejects/subs).
3. It produces the JSON/CSV + a review page.
4. To use the scripts directly: edit the `CONFIG` block at the top of
   `pull_pipeline.mjs`, run it in a browser-automation runtime, then
   `python3 scripts/build_review.py --data <slug>_pipeline.json --out review.html --title "..." --subtitle "..."`.

## Safety

- **No credential entry, ever.** If a browser isn't logged into Loxo, the agent
  stops and asks you to log in — it does not type passwords or bypass logins.
- **Reads are free; writes are gated.** Rejecting/cleanup requires your explicit
  go on the concrete list, logs a reason you approve, and tests on one record
  first. Rejecting moves a candidate to the Rejected stage (reversible), it does
  not delete anyone.
- **Duplicates are respected.** Loxo often holds duplicate person records; the
  workflow never auto-rejects a record whose name matches a keeper.

## License

MIT — see [LICENSE](LICENSE).

---

*Built from real recruiting workflow at Top Tier Talent Group. Not affiliated
with or endorsed by Loxo.*
