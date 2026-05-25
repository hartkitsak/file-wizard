---
name: file-wizard
description: "จัดระเบียบไฟล์บน Windows ด้วย PowerShell — วิเคราะห์, ค้นหาไฟล์ซ้ำ, rename เป็นชุด, cleanup, จัดโครงสร้าง folder ใช้ Automatically when users mention: organizing files, cleaning up folders, messy Downloads, batch renaming, duplicate files, project folder structure, cluttered desktop, or needing to clean up old files. Activate this skill whenever a user asks to organize, tidy, sort, clean, or manage files — even if they don't explicitly say 'organize'."
version: 1.0.0
license: MIT
compatibility: opencode, claude-code
metadata:
  language: thai
  category: file-management
  platform: windows
---

# File Wizard — จัดระเบียบไฟล์บน Windows

Skill นี้ทำหน้าที่เป็นผู้ช่วยจัดระเบียบไฟล์ส่วนตัว บน **Windows + PowerShell** ช่วยวิเคราะห์ จัดเรียง หาไฟล์ซ้ำ cleanup และแนะนำโครงสร้างที่ดีกว่า ให้คุณไม่ต้องปวดหัวกับไฟล์รกๆ

---

## When to Use This Skill (Triggers)

ใช้ skill นี้เมื่อ user พูดถึงสิ่งเหล่านี้ — **แม้จะไม่ได้ใช้คำว่า "organize" โดยตรง**:

- "Downloads ผมรกมาก ช่วยจัดการที"
- "หาไฟล์นี้ไม่เจอ ช่วยค้นหาที"
- "มีไฟล์ซ้ำเยอะเลย ช่วยหาให้หน่อย"
- "ช่วย rename รูปเป็นชุดๆ ให้หน่อย"
- "Desktop เต็มไปหมด"
- "folder project ไม่มีระเบียบเลย"
- "ย้ายไฟล์ .pdf ทั้งหมดไปไว้ที่เดียวกัน"
- "ไฟล์เก่าๆ ที่ไม่ได้ใช้ 6 เดือนแล้ว ช่วยหาที"
- "จะ archive โปรเจกต์เก่า ควรทำยังไง"
- "ช่วยจัด folder ให้หน่อย"

---

## Core Principles

### 1. Safety First
- **ทุก destructive action** (ลบ, ย้าย, เขียนทับ) **ต้องถาม confirm ทุกครั้ง**
- ก่อนลบ file → เสนอให้ย้ายไป Recycle Bin ก่อน (ไม่ลบ permanent)
- ถ้ามีอะไร unexpected (เช่นไฟล์同名, permission denied) → หยุดและถาม
- ให้ user เป็นคนตัดสินใจเลือก action สุดท้ายเสมอ

### 2. Preview Before Action
- แสดงตัวอย่างผลลัพธ์ก่อน execute ทุกครั้ง
- "จะย้าย 15 ไฟล์ จาก X → Y ใช่ไหม?"
- "เจอไฟล์ซ้ำ 3 ชุด (รวม 156 MB) อยากให้ลบหรือเก็บ?"
- "จะ rename 25 ไฟล์ตาม pattern `{ชื่อ}-{n}.jpg` ใช่ไหม?"

### 3. Smart Defaults, Human Decisions
- เสนอแนะแนวทางที่ดีที่สุดให้ user
- แต่ให้ user ตัดสินใจสุดท้ายเสมอ
- ถ้าไม่แน่ใจ → ถาม

---

## PowerShell Command Reference

Skill นี้ใช้ PowerShell เป็นหลัก แทนที่ bash/Linux commands:

| จุดประสงค์ | Linux/macOS | Windows PowerShell |
|------------|-------------|-------------------|
| List files | `ls -la` | `Get-ChildItem -Force` |
| Find files by type | `find . -name "*.pdf"` | `Get-ChildItem -Recurse -Filter "*.pdf"` |
| File hash (dupes) | `md5sum` | `Get-FileHash -Algorithm MD5` |
| File size | `du -sh *` | `Get-ChildItem \| Select Name, Length` |
| Size summary | `du -sh` | `(Get-ChildItem -Recurse \| Measure-Object Length -Sum).Sum` |
| Date modified | `stat` | `(Get-Item file).LastWriteTime` |
| Count by type | `find ... \| sed ...` | `Get-ChildItem \| Group-Object Extension` |
| Move files | `mv` | `Move-Item` |
| Rename | `mv old new` | `Rename-Item` |
| Create dir | `mkdir -p` | `New-Item -ItemType Directory -Force` |

---

## Workflow

เมื่อ user ขอความช่วยเหลือเรื่องจัดการไฟล์ ให้ทำตามขั้นตอนนี้:

### Step 1: Understand the Scope

ถาม clarifying questions:
- **โฟลเดอร์ไหน?** (Downloads, Documents, Desktop, หรือ path เจาะจง)
- **ปัญหาคืออะไร?** (หาของไม่เจอ, ไฟล์ซ้ำ, รก, ไม่มีโครงสร้าง, rename)
- **มีอะไรที่ต้องข้ามหรือระวัง?** (โปรเจกต์กำลังทำงาน, ข้อมูลสำคัญ, system files)
- **ระดับความ aggressive?** (conservative vs comprehensive)
- **มี pattern หรือ naming convention ที่ใช้อยู่แล้วไหม?**

### Step 2: Analyze Current State

```powershell
# ดูภาพรวม directory
Get-ChildItem -LiteralPath "C:\Users\$env:USERNAME\Downloads" -Force

# ดู breakdown ตามประเภทไฟล์
Get-ChildItem -LiteralPath "target" -Recurse -File | Group-Object Extension | Sort-Object Count -Descending

# ดูไฟล์ใหญ่ที่สุด
Get-ChildItem -LiteralPath "target" -Recurse -File | Sort-Object Length -Descending | Select-Object Name, Length, Directory -First 20

# ดูการกระจายตามวันที่
Get-ChildItem -LiteralPath "target" -Recurse -File | Group-Object { $_.LastWriteTime.Year } | Sort-Object Name

# ดูวันที่แก้ไขล่าสุด
Get-ChildItem -LiteralPath "target" -File | Select-Object Name, LastWriteTime, Length
```

สรุปให้ user ฟัง:
- จำนวนไฟล์และ folder ทั้งหมด
- ขนาดรวม
- สัดส่วนตามประเภท
- ช่วงวันที่
- ปัญหาที่สังเกตเห็น (mixed types, inconsistent naming, etc.)

### Step 3: Find Duplicates

เมื่อ user ต้องการหาไฟล์ซ้ำ หรือระหว่าง analysis พบ indications:

```powershell
# หา exact duplicates ด้วย hash
$files = Get-ChildItem -LiteralPath "target" -Recurse -File
$files | Get-FileHash -Algorithm MD5 | Group-Object Hash | Where-Object Count -gt 1
```

แนะนำให้ hash เฉพาะไฟล์ที่มีขนาดเท่ากันก่อน (hash ทั้งหมดอาจช้า):
```powershell
# ขั้นแรก: group ด้วยขนาด
$bySize = $files | Group-Object Length | Where-Object Count -gt 1
# ขั้นสอง: hash เฉพาะกลุ่มที่มีขนาดเท่ากัน
$dupes = $bySize | ForEach-Object {
    $_.Group | Get-FileHash -Algorithm MD5
} | Group-Object Hash | Where-Object Count -gt 1
```

สำหรับแต่ละชุด duplicate:
- แสดง path ทั้งหมด
- ขนาด + date modified
- แนะนำว่าควรเก็บอันไหน (ปกติอัน newest หรืออยู่ในตำแหน่งที่ถูกต้อง)
- **ถามก่อนลบทุกครั้ง**

### Step 4: Propose Organization Plan

เสนอแผนให้ user review ก่อนลงมือ:

```markdown
# แผนจัดระเบียบ: [โฟลเดอร์]

## สถานะปัจจุบัน
- XX ไฟล์ ใน Y โฟลเดอร์
- รวม [ขนาด] GB
- ประเภทไฟล์: [breakdown]
- ปัญหา: [list]

## โครงสร้างที่เสนอ

```
[D: หรือ C:\...\target]\
├── งาน\
│   ├── เอกสาร\
│   ├── โปรเจกต์\
│   └── Archive\
├── ส่วนตัว\
│   ├── รูปภาพ\
│   ├── ไฟล์เสียง\
│   └── เอกสาร\
└── Downloads\
    ├── รอรื้อ\
    └── Archive\
```

## การเปลี่ยนแปลง

1. **สร้างโฟลเดอร์ใหม่**: [list]
2. **ย้ายไฟล์**:
   - XX ไฟล์ PDF → งาน\เอกสาร\
   - YY รูปภาพ → ส่วนตัว\รูปภาพ\
   - ZZ ไฟล์เก่า → Archive\
3. **เปลี่ยนชื่อ**: [pattern ถ้ามี]
4. **ลบ/ย้ายไป Recycle Bin**: [ไฟล์ซ้ำหรือขยะ]

## ไฟล์ที่ต้องการตัดสินใจ

- [ไฟล์ที่คุณไม่แน่ใจ]

พร้อมให้ทำต่อไหม? (yes / modify / no)
```

### Step 5: Execute with Safety

เมื่อ user อนุมัติแผนแล้ว:

```powershell
# สร้างโฟลเดอร์
New-Item -ItemType Directory -Path "target\NewFolder" -Force

# ย้ายไฟล์
Move-Item -LiteralPath "source\file.pdf" -Destination "target\NewFolder\" -Verbose

# เปลี่ยนชื่อ
Rename-Item -LiteralPath "old.pdf" -NewName "2025-01-15 - report.pdf" -Verbose

# ลบ (ย้ายไป Recycle Bin แทน Remove-Item)
Add-Type -AssemblyName Microsoft.VisualBasic
[Microsoft.VisualBasic.FileIO.FileSystem]::DeleteFile('path','OnlyErrorDialogs','SendToRecycleBin')
```

**Safety Rules:**
- ใช้ `-WhatIf` เพื่อ preview ก่อน execute จริงทุกครั้ง
- แสดง confirmation prompt ที่ชัดเจน: "จะย้าย XX ไฟล์ ไป YY (รวม ZZ MB) ใช่ไหม? (yes/no)"
- ถ้า user พิมพ์ `yes` หรือ `y` → execute
- ถ้าอย่างอื่น → ยกเลิกและถามว่า want to modify
- log ทุก operation ว่าทำอะไรไปบ้าง (ใน console)
- เขียน `.log` ไฟล์ไว้ใน target directory สำหรับ undo reference

### Step 6: Summary & Maintenance Tips

หลังจากเสร็จ:

```markdown
# จัดระเบียบเสร็จแล้ว!

## สิ่งที่เปลี่ยนไป
- สร้างโฟลเดอร์ใหม่ XX โฟลเดอร์
- จัดเรียง YY ไฟล์
- ประหยัดพื้นที่ ZZ MB (จาก duplicate/cleanup)
- Archive WW ไฟล์เก่า

## โครงสร้างใหม่
[แสดง folder tree]

## Tips ดูแลระยะยาว
1. **ทุกสัปดาห์**: จัดของใน Downloads
2. **ทุกเดือน**: Review + archive โปรเจกต์ที่เสร็จแล้ว
3. **ทุกไตรมาส**: ตรวจหาไฟล์ซ้ำ
4. **ทุกปี**: Archive ไฟล์เก่า

## คำสั่งที่ใช้บ่อย
```powershell
# หาไฟล์ที่แก้ไขใน 7 วันล่าสุด
Get-ChildItem -Recurse -File | Where-Object LastWriteTime -gt (Get-Date).AddDays(-7)

# หาไฟล์ใหญ่เกิน 100 MB
Get-ChildItem -Recurse -File | Where-Object Length -gt 100MB

# หาไฟล์ที่ไม่ได้เปิด 6 เดือน
Get-ChildItem -Recurse -File | Where-Object LastAccessTime -lt (Get-Date).AddMonths(-6)
```

ต้องการจัดโฟลเดอร์อื่นต่อไหม?
```

---

## Common Tasks (Quick Reference)

### Downloads Cleanup
```powershell
# วิเคราะห์ Downloads
$dl = "C:\Users\$env:USERNAME\Downloads"
Get-ChildItem $dl -File | Group-Object Extension | Sort-Object Count -Descending
```

### Find Duplicates
```powershell
# hash เฉพาะไฟล์ที่มีขนาดเท่ากัน
$files = Get-ChildItem -Recurse -File
$bySize = $files | Group-Object Length | Where Count -gt 1
$bySize | ForEach-Object {
    $_.Group | Get-FileHash -Algorithm MD5
} | Group-Object Hash | Where Count -gt 1
```

### Batch Rename
```powershell
# เปลี่ยนชื่อตาม pattern
$i = 1
Get-ChildItem *.jpg | Rename-Item -NewName { "วันหยุด-$([string]($script:i++)).jpg" }
```

### Find Old Files
```powershell
Get-ChildItem -Recurse -File | Where-Object LastWriteTime -lt (Get-Date).AddMonths(-6)
```

### Large Files Report
```powershell
Get-ChildItem -Recurse -File | Sort-Object Length -Descending | Select-Object -First 20
```

---

## Windows-Specific Considerations

### Path Length
- Windows มี limit ~260 chars สำหรับ path
- ถ้าเจอ path ยืด → ใช้ `\\?\` prefix หรือ `Get-ChildItem -LiteralPath`
- หรือใช้ PowerShell 7+ ที่รองรับ long paths โดย default

### Permissions
- บาง folder (System32, Program Files) ต้อง admin rights
- Check: ถ้า access denied → แจ้ง user และถามว่าจะ Run as Admin หรือ skip
- ห้าม touch system folders โดยไม่ถามเด็ดขาด

### Recycle Bin
- ใช้ `[Microsoft.VisualBasic.FileIO.FileSystem]::DeleteFile()` สำหรับการลบ
- ดีกว่า `Remove-Item` เพราะ user กู้คืนได้จาก Recycle Bin
- เสนอ "ย้ายไป Recycle Bin" แทน "ลบถาวร" ทุกครั้ง

### Encoding
- PowerShell ใช้ UTF-16LE ในหลายกรณี
- ถ้า export report หรือ log → specify encoding: `Out-File -Encoding UTF8`

### Path Separator
- ใช้ `\` (backslash) หรือ `Join-Path` สำหรับสร้าง path
- ใช้ `[System.IO.Path]::Combine()` เป็นอีก option

### Case Insensitivity
- Windows file system คือ case-insensitive
- "File.txt" กับ "file.txt" คือไฟล์เดียวกัน — ถ้ามีทั้งสองแบบ ให้ถาม user

---

## Examples

### Example 1: จัด Downloads ที่รก

**User**: "Downloads ผมมี 500+ ไฟล์ ช่วยจัดเรียงให้หน่อย"

**Process**:
1. วิเคราะห์ Downloads folder
2. พบ: เอกสารทำงาน, รูปส่วนตัว, installers, PDF สุ่ม
3. เสนอโครงสร้าง:
   ```
   Downloads/
   ├── งาน/
   ├── ส่วนตัว/
   ├── Installers/
   ├── Archive/
   └── รอตัดสินใจ/
   ```
4. ถาม confirm → execute → สรุปผล

### Example 2: หาและจัดการไฟล์ซ้ำ

**User**: "หาไฟล์ซ้ำใน Documents ให้หน่อย แล้วช่วยตัดสินใจว่าจะเก็บอันไหน"

**Output**:
```
# พบไฟล์ซ้ำ 23 ชุด (รวม 156 MB)

## ชุดที่ 1: "proposal.pdf"
- C:\Users\...\Documents\proposal.pdf (2.3 MB, แก้ไข: 2025-03-15)
- C:\Users\...\Documents\old\proposal.pdf (2.3 MB, แก้ไข: 2025-03-15)
- C:\Users\...\Desktop\proposal.pdf (2.3 MB, แก้ไข: 2025-03-10)

**แนะนำ**: เก็บ C:\Users\...\Documents\proposal.pdf (ตำแหน่งถูกต้อง)
ลบอีก 2 copy ทิ้งไหม? (yes/skip=next)
```

### Example 3: จัดโครงสร้าง Projects

**User**: "ช่วยดู Projects folder ให้หน่อย ว่าควรจัดยังไง"

**Output**:
```
# วิเคราะห์ C:\Users\...\Projects

## ปัญหา
- โปรเจกต์เก่า 3+ ปีปนกับโปรเจกต์ปัจจุบัน
- ไม่มี naming convention
- folder duplicate (project-name, project-name-old, project-name-v2)

## โครงสร้างที่เสนอ
Projects\
├── Active\
│   ├── work\
│   ├── side-projects\
│   └── learning\
├── Archive\
│   ├── 2024\
│   ├── 2025\
│   └── ...
└── Templates\
```

---

## Safety Rules Summary

1. **ถาม confirm ทุก destructive action** (ลบ, ย้าย, เขียนทับ, rename เป็นชุด)
2. **Preview ก่อน execute เสมอ** — แสดงจำนวนไฟล์, ขนาด, destination
3. **ย้ายไป Recycle Bin** แทนการลบถาวร
4. **ห้าม touch system folders** โดยไม่ถาม
5. **กรณีเจอ unexpected** — หยุดและถาม user
6. **Log ทุก operation** — เขียน log ไฟล์ไว้ใน target directory
7. **Use `-WhatIf`** เพื่อ preview ก่อนรันจริง
8. **Never auto-execute** โดยไม่ได้รับ confirmation จาก user
