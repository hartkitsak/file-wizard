# 6-Step Workflow

เมื่อ user ขอความช่วยเหลือเรื่องจัดการไฟล์ ให้ทำตามนี้:

## Step 1: Understand Scope
ถาม clarifying questions:
- **โฟลเดอร์ไหน?** (Downloads, Documents, Desktop, หรือ path เจาะจง)
- **ปัญหาคืออะไร?** (หาของไม่เจอ, ไฟล์ซ้ำ, รก, rename)
- **มีอะไรต้องข้าม?** (โปรเจกต์กำลังทำงาน, system files)
- **ระดับ aggressive?** (conservative vs comprehensive)

## Step 2: Analyze Current State
```powershell
# ดูภาพรวม
Get-ChildItem -LiteralPath "target" -Force

# Breakdown ตามประเภท
Get-ChildItem -Recurse -File | Group-Object Extension | Sort-Object Count -Descending

# ไฟล์ใหญ่ที่สุด 20
Get-ChildItem -Recurse -File | Sort-Object Length -Descending | Select-Object Name, Length, Directory -First 20

# การกระจายตามปี
Get-ChildItem -Recurse -File | Group-Object { $_.LastWriteTime.Year } | Sort-Object Name
```

สรุป: จำนวนไฟล์, ขนาดรวม, สัดส่วนตามประเภท, ปัญหาที่เจอ

## Step 3: Find Duplicates
```powershell
# Group ด้วยขนาด → hash เฉพาะกลุ่มที่ขนาดเท่ากัน
$files = Get-ChildItem -Recurse -File
$bySize = $files | Group-Object Length | Where-Object Count -gt 1
$dupes = $bySize | ForEach-Object {
    $_.Group | Get-FileHash -Algorithm MD5
} | Group-Object Hash | Where-Object Count -gt 1
```

สำหรับแต่ละชุด: แสดง path, ขนาด, date → แนะนำว่าควรเก็บอันไหน → **ถามก่อนลบ**

## Step 4: Propose Organization Plan
เสนอแผนให้ review ก่อนลงมือ:
1. สร้างโฟลเดอร์ใหม่
2. ย้ายไฟล์ตามประเภท
3. เปลี่ยนชื่อ (ถ้ามี pattern)
4. ลบ/ย้ายไป Recycle Bin (ไฟล์ซ้ำ/ขยะ)

รอ user: `yes` / `modify` / `no`

## Step 5: Execute with Safety
```powershell
New-Item -ItemType Directory -Path "target\NewFolder" -Force
Move-Item -LiteralPath "source\file.pdf" -Destination "target\NewFolder\" -Verbose
Rename-Item -LiteralPath "old.pdf" -NewName "2025-01-15 - report.pdf" -Verbose

# ลบ (Recycle Bin)
Add-Type -AssemblyName Microsoft.VisualBasic
[Microsoft.VisualBasic.FileIO.FileSystem]::DeleteFile('path','OnlyErrorDialogs','SendToRecycleBin')
```

- ใช้ `-WhatIf` ก่อน execute จริงทุกครั้ง
- Ask confirm ทุก batch action
- Log ทุก operation

## Step 6: Summary
หลังจากเสร็จ: สรุปสิ่งที่เปลี่ยนไป, โครงสร้างใหม่, tips ดูแลระยะยาว

## Windows-Specific
- Path length: ใช้ `\\?\` prefix หรือ PowerShell 7+
- Permissions: ถ้า denied → แจ้ง user ถาม Run as Admin หรือ skip
- Encoding: `Out-File -Encoding UTF8`
- Case-insensitive: "File.txt" == "file.txt" → ถ้ามีทั้งสอง ให้ถาม user
