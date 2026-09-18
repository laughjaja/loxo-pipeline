# AGENTS.md

Entry point for AI coding/automation agents (Claude, Cursor, Codex, Aider,
custom agents) that land in this repo.

**What this is:** a self-contained, vendor-neutral skill for working a Loxo ATS
job pipeline — pull candidates + resumes + activity, build a review page, and
optionally clean up (bulk reject).

**Start here:** read [`SKILL.md`](SKILL.md). It is the full playbook and holds
the guardrails. [`reference/loxo-endpoints.md`](reference/loxo-endpoints.md) has
the concrete Loxo endpoints, workflow-stage IDs, and activity-type IDs.

**How to run it:**
- If your framework loads skills, this folder *is* the skill (`SKILL.md` has YAML
  frontmatter with `name` + `description` for auto-triggering).
- Otherwise, follow `SKILL.md` as a playbook and use `scripts/` as a reference
  implementation, swapping the transport layer for whatever browser-automation
  or HTTP tool you have.

**Non-negotiable guardrails (see SKILL.md for detail):**
- Never enter credentials or bypass a login wall. If not logged into Loxo, stop
  and ask the user to log in.
- Reads are free; every write (reject/stage change) needs the user's explicit
  go on the exact list, an approved reason note, and a test-of-one first.
- Keep large list payloads on disk; never paste them into the model context.
- Respect duplicate person records; never auto-reject a record matching a keeper.
