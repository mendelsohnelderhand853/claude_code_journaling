---
description: Generate comprehensive monthly review from 6 perspectives with themed synthesis
allowed-tools:
  - Task
  - Glob
  - Read
---

# Monthly Review Command

Generate a comprehensive monthly review by processing journals through 6 perspectives in parallel, then synthesizing into a themed final report.

## Input

Month identifier from: $ARGUMENTS

**Default**: If no argument provided, use the last completed month (e.g., if today is February, process January).

**Accepted formats**:
- `YYYY-MM` (e.g., "2026-01")
- Month name + year (e.g., "January 2026", "Jan 2026")
- Just month name for current year (e.g., "January")

## Execution Steps

### 1. Determine Target Month

Parse arguments to get `YYYY-MM` format.

If no arguments: Calculate last completed month based on current date.

### 2. Verify Journals Exist

Use Glob to check `06 Agenda/Journal/YYYY-MM-*.md` files exist for target month.

If no journals found: Report error and stop.

### 3. Create Output Directories

Ensure these directories exist (check with Glob, create if needed):
- `07 Context/Analysis/therapist/`
- `07 Context/Analysis/coach/`
- `07 Context/Analysis/strengths/`
- `07 Context/Analysis/values-meaning/`
- `07 Context/Analysis/relationships/`
- `07 Context/Analysis/chronicle/`

### 4. Launch 6 Perspective Subagents in Parallel

Use the Task tool with `subagent_type: monthly-processor` to launch **all 6 in a single message** (parallel execution):

```
1. "Process YYYY-MM as therapist"
2. "Process YYYY-MM as coach"
3. "Process YYYY-MM as strengths"
4. "Process YYYY-MM as values-meaning"
5. "Process YYYY-MM as relationships"
6. "Process YYYY-MM as chronicle"
```

**CRITICAL**: Launch all 6 in a single message with 6 separate Task tool calls to maximize parallelism.

### 5. Wait for All Perspectives to Complete

All 6 subagents must complete before proceeding.

### 6. Launch Synthesis Subagent

Use the Task tool with `subagent_type: monthly-synthesis`:

```
"Synthesize YYYY-MM"
```

### 7. Report Completion

After synthesis completes, report:

```
Monthly review for [Month Year] complete!

Perspective analyses created:
- 07 Context/Analysis/therapist/YYYY-MM-therapist.md
- 07 Context/Analysis/coach/YYYY-MM-coach.md
- 07 Context/Analysis/strengths/YYYY-MM-strengths.md
- 07 Context/Analysis/values-meaning/YYYY-MM-values-meaning.md
- 07 Context/Analysis/relationships/YYYY-MM-relationships.md
- 07 Context/Analysis/chronicle/YYYY-MM-chronicle.md

Final report: [[06 Agenda/Monthly/YYYY-MM]]
```

## Error Handling

- If no journals for target month: "No journal entries found for [month]. Cannot generate review."
- If perspective fails: Note which perspective failed, continue with others, warn in final output
- If synthesis fails: Report error, note that perspective files were still created

## Notes

- This command orchestrates existing agents - it doesn't process journals directly
- The monthly-processor agent handles each perspective
- The monthly-synthesis agent creates the final themed report
- All perspective analyses are preserved as separate files for future reference
