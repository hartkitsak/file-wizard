# File Wizard

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

File Wizard is a Claude Code skill for organizing files on Windows using PowerShell. It analyzes folder structures, finds duplicates, batch renames files, and cleans up clutter — all with safety guarantees.

---

## About This Skill

File Wizard teaches Claude how to inspect and reorganize Windows file systems through PowerShell. It covers:

- **Analysis** — Scan folders and break down content by type, size, and age
- **Deduplication** — Find duplicate files using MD5 hashing with smart retention suggestions
- **Batch rename** — Rename files by pattern or EXIF metadata (e.g., DSC_0123.jpg → beach-2025-01-15-001.jpg)
- **Cleanup** — Identify old files, trash, and duplicates for archiving or removal
- **Safety** — Every destructive action requires confirmation and uses the Recycle Bin

The skill follows a structured workflow: understand the user's intent, analyze, propose a plan, confirm, execute, and summarize.

---

## Usage

Open Claude Code in the target directory and describe what you need:

```
ช่วยจัดเรียงไฟล์ใน Downloads ให้หน่อยครับ มีทั้ง PDF รูป installer ปนกันหมด
```

```
หาไฟล์ซ้ำใน Documents ให้หน่อย น่าจะมีหลายชุดที่โหลดซ้ากัน
```

```
ช่วย rename รูปใน folder รูปทริปทะเล
จาก DSC_0001.jpg → ทะเล-วันที่-ลำดับ ตามวันที่ถ่ายใน EXIF
```

```
หาไฟล์เก่าในโปรเจกต์ folder ที่ไม่ได้แตะเกิน 1 ปีหน่อย
จะได้ archive หรือลบทิ้ง
```

---

## Installation

### Claude Code
Clone the repository to your skills directory:
```
git clone https://github.com/hartkitsak/file-wizard.git ~/.claude/skills/file-wizard
```

### Other tools
Clone anywhere and add the path to your configuration.

**Requirements:** Windows 10/11, PowerShell 7+

---

## License

MIT — free to use, modify, and share.

Built by [hartkitsak](https://github.com/hartkitsak)
