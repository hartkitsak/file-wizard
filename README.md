# File Wizard 🧙

**จัดระเบียบไฟล์บน Windows ด้วย PowerShell** — วิเคราะห์โฟลเดอร์, ค้นหาไฟล์ซ้ำ, rename เป็นชุด, cleanup, จัดโครงสร้างใหม่ ให้คุณไม่ต้องปวดหัวกับไฟล์รกๆ

## Features

- 🔍 วิเคราะห์โครงสร้างโฟลเดอร์ — ขนาด, ประเภทไฟล์, การกระจายตามวันที่
- 🔁 ค้นหาไฟล์ซ้ำด้วย MD5 hash
- ✏️ Batch rename ตาม pattern
- 🗑️ ลบแบบปลอดภัย (ส่งไป Recycle Bin, ไม่ลบถาวร)
- 📁 เสนอโครงสร้างโฟลเดอร์ที่เหมาะสม
- 🛡️ Safety-first — ถาม confirm ทุก destructive action

## Installation

```powershell
git clone https://github.com/hartkitsak/file-wizard.git "$HOME\.config\opencode\skills\file-wizard"
```

> สำหรับ Claude Code: วาง skill ไว้ใน `~/.config/opencode/skills/` เพื่อให้ detect อัตโนมัติ

## Usage

```
/file-wizard ช่วยจัด Downloads ให้หน่อย
/file-wizard หาไฟล์ซ้ำใน Documents
/file-wizard rename รูปเป็นชุด
/file-wizard จัด folder Projects
```

## Structure

```
file-wizard/
├── SKILL.md                    ← Entry point (อ่านอัตโนมัติ)
├── workflow/workflow.md        ← 6 Step: scope → analyze → plan → execute → summary
├── reference/
│   ├── powershell-commands.md  ← PowerShell command cheat sheet
│   └── safety-rules.md         ↑ Safety rules detail
└── examples/
    └── examples.md             ← ตัวอย่าง scenarios
```

## Example

**User**: "Downloads ผมรก ช่วยจัดการที"

**Agent จะ**: วิเคราะห์ → เสนอโครงสร้าง (งาน/ส่วนตัว/Archive) → ถาม confirm → execute → สรุปผล

## Requirements

- [Claude Code](https://claude.ai) หรือ [opencode](https://opencode.ai)
- Windows + PowerShell 7+

## License

MIT
