---
name: file-wizard
description: "จัดระเบียบไฟล์บน Windows ด้วย PowerShell — วิเคราะห์, ค้นหาไฟล์ซ้ำ, rename เป็นชุด, cleanup, จัดโครงสร้าง folder"
license: MIT
compatibility: Designed for Claude Code and opencode
metadata:
  language: thai
  category: file-management
  platform: windows
  version: "1.0.0"
---

# File Wizard — จัดระเบียบไฟล์บน Windows

## Triggers
- Downloads/Desktop รก, หาไฟล์ไม่เจอ, ไฟล์ซ้ำ, rename เป็นชุด, จัด folder, archive โปรเจกต์, cleanup

## Files
| ไฟล์ | เมื่อไหร่ |
|------|----------|
| `workflow/workflow.md` | ขั้นตอน 6 Step (วิเคราะห์→วางแผน→execute) |
| `reference/powershell-commands.md` | PowerShell command cheat sheet |
| `reference/safety-rules.md` | Safety rules detail |
| `examples/examples.md` | ตัวอย่าง scenarios การใช้งาน |

## Safety (Core Rules)
1. **ถาม confirm ทุก destructive action** — ลบ, ย้าย, เขียนทับ, batch rename
2. **Preview ก่อน execute** — แสดงจำนวน ขนาด destination
3. **ย้ายไป Recycle Bin** → แทนลบถาวร
4. **Use `-WhatIf`** ก่อนรันจริงทุกครั้ง
5. **ห้าม touch system folders** โดยไม่ถาม
6. **Log ทุก operation** → ไว้ใน target directory

## PowerShell vs Linux
| Purpose | Linux | PowerShell |
|---------|-------|-----------|
| List | `ls -la` | `Get-ChildItem -Force` |
| Find | `find -name` | `Get-ChildItem -Recurse -Filter` |
| Hash | `md5sum` | `Get-FileHash -Algorithm MD5` |
| Size | `du -sh` | `Measure-Object Length -Sum` |
| Move | `mv` | `Move-Item` |
| Rename | `mv old new` | `Rename-Item` |
| Recycle | `trash` | `[Microsoft.VisualBasic.FileIO]::DeleteFile(..., 'SendToRecycleBin')` |
