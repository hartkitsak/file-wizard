# Safety Rules — File Organizer

These rules exist to prevent data loss and build trust. Follow them strictly.

---

## Golden Rule

**Never modify files without explicit human confirmation.**

Every destructive action (delete, move, rename overwrite, batch operation) requires:
1. A clear preview of what will happen
2. A yes/no confirmation from the user
3. Execution only after "yes" or "y"

---

## Confirmation Flow

```
User: "Organize my Downloads"
  ↓
Step 1: Analyze & propose plan
Step 2: Show plan to user → "Ready? (yes/modify/no)"
  ↓ (user says "yes")
Step 3: For EACH batch action:
       "Move 15 PDFs → Work\Documents\ ? (yes/no)"
  ↓ (user says "yes")
Step 4: Execute, log, confirm success
```

---

## What Counts as Destructive

- **DELETE** (permanent or Recycle Bin) → ALWAYS confirm
- **MOVE** (change location) → ALWAYS confirm
- **RENAME** (especially batch) → ALWAYS confirm
- **OVERWRITE** (file exists at destination) → ALWAYS confirm + show both files
- **MERGE folders** → ALWAYS confirm with preview

---

## What Is Safe (No Confirm Needed)

- **READING** files (listing, analyzing, hashing)
- **PREVIEWING** (using -WhatIf to show what would happen)
- **SHOWING** suggestions/plans (user hasn't approved yet)
- **CREATE empty folders** (no data loss risk)

---

## Windows-Specific Safety

| Situation | Action |
|-----------|--------|
| Permission denied | Stop and tell user. Suggest Run as Admin or skip. |
| System folder detected | Never touch without explicit + emphatic user confirmation |
| File in use by another process | Skip and report to user |
| Path too long (>260 chars) | Use `\\?\` prefix or PowerShell 7+ |
| Hidden/system file encountered | Flag to user before any action |
| Admin rights needed | Ask user: "Run as Admin or skip?" |

---

## Duplicate Handling

When deleting duplicates:
1. Show all copies with paths, sizes, dates
2. Recommend which to keep (usually newest or best-located)
3. Let user decide per-set or batch: "Keep 1, delete rest?"
4. Delete to Recycle Bin, not permanent

---

## Undo / Recovery

- Always delete to Recycle Bin (never `Remove-Item`)
- Write a `.log` file in the target directory:
  ```
  [2025-05-25 12:34:56] Moved: 'D:\DL\report.pdf' → 'D:\DL\Work\Documents\report.pdf'
  [2025-05-25 12:35:01] Moved: 'D:\DL\photo.jpg' → 'D:\DL\Personal\Photos\photo.jpg'
  ```
- If user asks "can I undo", point them to the log file

---

## When Things Go Wrong

| Problem | Response |
|---------|----------|
| File not found | Double-check path, list directory, ask user |
| Access denied | "Permission denied — run as admin or skip this file?" |
| Unexpected file type | Ask user before moving |
| Pattern match too broad | Show preview, ask to narrow down |
| Very large operation (1000+ files) | Warn user: "This affects 1,234 files. Continue?" |
