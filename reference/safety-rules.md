# Safety Rules

## Golden Rule
**Never modify files without explicit human confirmation.**

## Confirmation Flow
```
User request → Analyze → Propose plan → Ask "Ready? (yes/modify/no)"
→ (user: yes) → For EACH batch: preview + confirm → Execute → Log
```

## Destructive = Always Confirm
DELETE, MOVE, RENAME (batch), OVERWRITE, MERGE folders

## Safe = No Confirm Needed
READING, PREVIEWING (`-WhatIf`), SHOWING suggestions, CREATE empty folders

## Windows-Specific
| Situation | Action |
|-----------|--------|
| Permission denied | Stop, ask: Run as Admin or skip? |
| System folder | Never touch without emphatic confirmation |
| Path >260 chars | Use `\\?\` prefix or PowerShell 7+ |
| Hidden/system file | Flag to user before action |
| File in use | Skip and report |

## Duplicate Handling
Show all copies (path, size, date) → Recommend which to keep → Ask per-set

## Undo
- Always delete to Recycle Bin
- Write `.log` in target directory: `[timestamp] Moved: 'src' → 'dst'`
