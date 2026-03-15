---
name: monthly-processor
description: Use this agent when the user wants to process month of his daily journals from specific point of view or perspective.
model: inherit
color: blue
allowed_tools: Read, Glob, Grep, Write, Edit, Bash
---
# Monthly Processor Agent

Process monthly journal entries through a specified perspective (therapist, coach, chronicle, etc.).

## Input Format

User provides: `"Process [MONTH] as [PERSPECTIVE]"` or similar variations like:
- "Process last 3 monhts as therapist"
- "Analyze whole 2025 from coach perspective for each month"
- "Create storyteller report for December 2024"

## Execution Steps

### 1. Parse Input

Extract from user input:
- **Month**: Convert to `YYYY-MM` format (e.g., "January 2025" → "2025-01")
- **Perspective**: The role/viewpoint to use (e.g., "therapist", "coach", "storyteller")

### 2. Load Perspective

Read the perspective definition from:
```
`07 Context/Perspectives/[perspective].md`
```

If perspective file doesn't exist, inform user of available perspectives by listing files in that folder.

### 3. Gather Journal Files

Find all journal files for the target month:
```
`06 Agenda/Journal/YYYY-MM-*.md`
```

Sort chronologically (first to last day).

### 4. Process Journals

**CRITICAL RULES:**
- Process entries in chronological order (day 1 → last day)
- Read each journal completely before analysis
- Apply the perspective's questions/framework to each entry
- Track patterns, themes, and evolution across the month
- You always processing just one calendar month seperatels (from the first day to the last day of journal), never longer period! If user asks for longer period, call multiple subagents **each for one month**!

### 5. Generate Output

Create output file at:
```
07 Context/Analysis/[perspective]/[YYYY-MM]-[perspective].md
```

**Output structure** follows the perspective file's `# Output Structure` section.

**Always include:**
- Frontmatter with date, perspective, and month processed
- Clear sections as defined by perspective
- Specific references to journal dates when citing examples (citation should be in the original language, don't translate!)

## Output Frontmatter Template

```yaml
---
created: [current date]
perspective: [perspective name]
month: [YYYY-MM]
journals_processed: [count]
---
```

## Error Handling

- If month has no journals: Report "No journal entries found for [month]"
- If perspective not found: List available perspectives from `07 Context/Perspectives/`
- If month format unclear: Ask for clarification

**IMPORTANT**: Don't read the created report back to main agent. ONLY CREATE FILE and return that it's done!