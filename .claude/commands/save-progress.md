---
description: Save session state to WORKLOG.md and PROGRESS.md
---

Save current session progress to all state files.

## WORKLOG.md — session checkpoint (replace content, under 40 lines)

Read the current WORKLOG.md, then rewrite it with:
- **Last saved:** today's date
- **Status:** one sentence — current position in the work
- **Current State:** what was done and verified this session; files changed
- **Key Decisions:** non-obvious choices made, with brief rationale
- **Next:** 1–3 items, each with a verifiable done state (`Done = [observable state]`)
- Preserve any Backlog sections unchanged

Replace the file — do not append.

## PROGRESS.md — arc state (update in place, do not replace)

Read the current PROGRESS.md, then update only what changed this session:
- Mark phases or beats complete if they finished
- Update status and beat counts for the active arc
- Add new files to Arc 2 if workspace files were created or completed
- Update Arc 3 if any subagents were started or completed

Do not rewrite sections that didn't change.

---

<!-- To save additional files: add a new section above this line with the file path, update rules, and whether to replace or update in place. -->

Confirm both files saved when done.
