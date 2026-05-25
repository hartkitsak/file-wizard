# File Wizard

อัจฉริยะจัดระเบียบไฟล์บน Windows — Claude Code skill ที่ช่วยวิเคราะห์ จัดเรียง ค้นหาไฟล์ซ้ำ rename เป็นชุด cleanup และจัดการไฟล์ทั้งหมดผ่าน PowerShell

## Features

- **วิเคราะห์โครงสร้าง** — สแกน folder แสดง breakdown ตามประเภท ขนาด และอายุไฟล์
- **จัดเรียงอัตโนมัติ** — เสนอโครงสร้าง folder ที่เหมาะสม ย้ายไฟล์เข้าหมวด
- **ค้นหาไฟล์ซ้ำ** — hash MD5, แสดง duplicates พร้อมแนะนำว่าควรเก็บอันไหน
- **Batch rename** — เปลี่ยนชื่อเป็นชุดตาม pattern หรือ EXIF date
- **Cleanup** — หาไฟล์เก่า ขยะ duplicates เสนอลบหรือ archive
- **Safety first** — ทุก destructive action ถาม confirm + ย้ายไป Recycle Bin

## Requirements

- Windows 10/11
- PowerShell 7+ (recommended) or PowerShell 5.1
- Claude Code (opencode / claude-code)
- `Add-Type -AssemblyName Microsoft.VisualBasic` (มีมาให้ใน Windows)

## Installation

### 1. Clone หรือ download

```powershell
git clone https://github.com/hartkitsak/file-wizard.git
```

หรือ download ZIP แล้วแตกไฟล์

### 2. ติดตั้งเป็น Skill

**For opencode:** ใส่ path ของ skill ใน `opencode.json`:

```json
{
  "skills": [
    "D:\\path\\to\\file-wizard"
  ]
}
```

หรือ copy ไปที่ `.opencode/skills/`:

```powershell
Copy-Item -Recurse "D:\path\to\file-wizard" "$env:USERPROFILE\.opencode\skills\file-wizard"
```

## Usage

เปิด Claude Code ใน directory ที่ต้องการ แล้วพิมพ์:

```text
ช่วยจัดเรียงไฟล์ใน Downloads ให้หน่อย
```

```text
หาไฟล์ซ้ำใน Documents ให้หน่อย
```

```text
เปลี่ยนชื่อรูปเป็นทะเล-วันที่-ลำดับ
```

```text
หาไฟล์เก่าที่ไม่ได้ใช้เกิน 1 ปี
```

```text
Desktop ผมรกมาก ช่วยจัดการที
```

## How It Works

1. **Understand** — ถาม scope, ปัญหา, ข้อควรระวัง
2. **Analyze** — ใช้ PowerShell สแกน: ประเภท, ขนาด, อายุ, duplicates
3. **Plan** — เสนอโครงสร้าง + แสดง preview
4. **Confirm** — ถาม user ก่อนทุก destructive action
5. **Execute** — ย้าย, rename, ลบ (ไป Recycle Bin)
6. **Summary** — สรุปผล + maintenance tips

## Safety

- **ทุกการลบไป Recycle Bin** — ไม่มี Remove-Item โดยตรง
- **Preview เสมอ** — `-WhatIf` ก่อน execute
- **Confirm ทุกครั้ง** — user ต้องพิมพ์ `yes` ก่อนทำอะไร
- **Log operations** — เขียน log ไว้ใน target folder
- **ถามก่อน touch system folders**

## Project Structure

```
file-wizard/
├── SKILL.md                       # Main skill instructions
├── README.md
├── reference/
│   ├── powershell-commands.md     # PowerShell command reference
│   └── safety-rules.md           # Safety guidelines
└── evals/
    └── evals.json                 # Test prompts (for development)
```

## License

MIT
