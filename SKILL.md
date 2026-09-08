---
name: memory-digest
description: Generate human-readable weekly/monthly reports from an AI agent's memory corpus. Use when the user asks "what did my agent do this week", "generate a work report", "记忆周报", "述职报告", "weekly digest", "monthly summary of agent activity", or when a periodic review of agent work is wanted. Reads date-named daily memory logs and produces a digest with activity timeline, project momentum, open threads, and metric changes.
---

# Memory Digest

Turn an agent's daily memory logs into a report a human actually wants to read: what happened, which projects moved, what is still pending, and which numbers changed.

Most memory tooling stores and retrieves for the agent. This skill reads the same corpus *for the owner*.

## When to run

1. The user asks for a weekly/monthly digest of agent activity.
2. A scheduled cron wants a periodic "what has my agent been doing" report.
3. Before a planning conversation, to ground it in what actually happened.

## Procedure

### Step 1: Locate the corpus

The workspace root containing a `memory/` directory of date-named daily logs (`YYYY-MM-DD.md`), optionally a `MEMORY.md`.

### Step 2: Run the digest script

```bash
python3 scripts/memory_digest.py --memory-dir <workspace-root> --days 7
```

Flags: `--days N` (window, default 7), `--out report.md` (save to file), `--json` (machine-readable).

The script is read-only. It produces:

| Section | Meaning |
|---|---|
| Activity timeline | Per-day entry with section count, topics, and a one-line summary |
| Projects in motion | Project/topic names with momentum: 🆕 new, ▶️ ongoing, ⏸️ stalled |
| Open threads | Pending/to-confirm items not yet closed, with source date |
| Numbers in motion | Metrics (downloads, users, stars…) mentioned in the window |

### Step 3: Verify before sending

Open threads and metrics are extracted heuristically. Before presenting, check that pending items are genuinely unresolved (an item marked done later in the window may still appear). Fix or annotate as needed.

### Step 4: Deliver

Present the digest in the user's language. For weekly cadence, suggest wiring into a cron job that saves the report to a file and notifies via the user's channel.

## Notes

- Read-only: never edits memory files.
- Works with any corpus of date-named daily logs; language-agnostic (matches Chinese and English pending markers and metric units).
- If the window has zero entries, say so explicitly — it usually means the daily flush job died (see the companion memory-checkup skill).
- Companion skills: `memory-checkup` (consistency audit), `memory-slimmer` (size control).
