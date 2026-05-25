# 🧹 File Wizard — จัดระเบียบไฟล์บน Windows

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Version](https://img.shields.io/badge/version-1.0.0-green)]()
[![Platform](https://img.shields.io/badge/platform-Windows-blue)]()
[![Language](https://img.shields.io/badge/language-Thai-brightgreen)]()

> **จัดระเบียบไฟล์บน Windows ด้วย PowerShell — โดยไม่ต้องมานั่งเรียงเอง**  
> วิเคราะห์โครงสร้าง · ค้นหาไฟล์ซ้ำ · rename เป็นชุด · cleanup · safety first

Skill นี้ใช้กับ [opencode](https://opencode.ai), Claude Code หรือ AI agent ที่รองรับ custom skills ช่วยคุณจัดการไฟล์บน Windows ตั้งแต่ Downloads รก → ไฟล์ซ้ำ → rename รูป → archive โปรเจกต์เก่า

---

## 📖 สารบัญ

- [🧹 File Wizard คืออะไร](#-file-wizard-คืออะไร)
- [🚀 วิธีใช้ — ตัวอย่าง Prompt จริง](#-วิธีใช้--ตัวอย่าง-prompt-จริง)
- [⚡ 6 ขั้นตอนการทำงาน](#-6-ขั้นตอนการทำงาน)
- [🛡️ Safety First](#-safety-first)
- [📦 สิ่งที่ได้จาก skill นี้](#-สิ่งที่ได้จาก-skill-นี้)
- [📂 โครงสร้างโฟลเดอร์](#-โครงสร้างโฟลเดอร์)
- [🔗 ระบบที่เกี่ยวข้อง](#-ระบบที่เกี่ยวข้อง)
- [📥 ติดตั้ง](#-ติดตั้ง)
- [📄 License](#-license)

---

## 🧹 File Wizard คืออะไร

File Wizard คือ **ผู้ช่วยจัดระเบียบไฟล์บน Windows** ที่เข้าใจ PowerShell และรู้จัก Windows file system เป็นอย่างดี มันช่วยคุณ:

| คุณสมบัติ | รายละเอียด |
|----------|-----------|
| 📊 **วิเคราะห์โครงสร้าง** | สแกน folder แสดง breakdown ตามประเภท ขนาด อายุไฟล์ |
| 🗂️ **จัดเรียงอัตโนมัติ** | เสนอโครงสร้าง folder ที่เหมาะสม ย้ายไฟล์เข้าหมวด |
| 🔍 **ค้นหาไฟล์ซ้ำ** | hash MD5, แสดง duplicates พร้อมแนะนำว่าควรเก็บอันไหน |
| ✏️ **Batch rename** | เปลี่ยนชื่อเป็นชุดตาม pattern หรือ EXIF date |
| 🗑️ **Cleanup** | หาไฟล์เก่า ขยะ duplicates เสนอลบหรือ archive |
| 🛡️ **Safety first** | ทุก destructive action ถาม confirm + ย้ายไป Recycle Bin |

---

## 🚀 วิธีใช้ — ตัวอย่าง Prompt จริง

เปิด Claude Code ใน directory ที่ต้องการ แล้วพิมพ์:

### จัด Downloads

```
ช่วยจัดเรียงไฟล์ใน Downloads ให้หน่อยครับ มีทั้ง PDF รูป installer ปนกันหมด
```

### หาไฟล์ซ้ำ

```
หาไฟล์ซ้ำใน Documents ให้หน่อย น่าจะมีหลายชุดที่โหลดซ้ากัน
```

### Batch rename รูป

```
ช่วย rename รูปใน folder รูปทริปทะเล
จาก DSC_0001.jpg → ทะเล-วันที่-ลำดับ ตามวันที่ถ่ายใน EXIF
```

### Cleanup ไฟล์เก่า

```
หาไฟล์เก่าในโปรเจกต์ folder ที่ไม่ได้แตะเกิน 1 ปีหน่อย
จะได้ archive หรือลบทิ้ง
```

### Desktop รก

```
Desktop ผมรกมาก ช่วยจัดการที
```

### จัด folder ไม่เป็นระเบียบ

```
ช่วยดู Projects folder ให้หน่อย ว่าควรจัดโครงสร้างยังไง
```

---

## ⚡ 6 ขั้นตอนการทำงาน

| Step | ชื่อ | ทำอะไร |
|------|------|-------|
| 1 | **🎯 ทำความเข้าใจ** | ถาม scope, ปัญหา, ข้อควรระวัง, ระดับ aggressive |
| 2 | **🔬 วิเคราะห์** | ใช้ PowerShell สแกน: ประเภทไฟล์, ขนาด, อายุ, duplicates |
| 3 | **📋 เสนอแผน** | แสดงโครงสร้างที่เสนอ + preview การเปลี่ยนแปลง |
| 4 | **✅ ยืนยัน** | ถาม user ก่อนทุก destructive action — "ย้าย 15 ไฟล์ ใช่ไหม?" |
| 5 | **⚙️ ดำเนินการ** | สร้าง folder, ย้าย, rename, ลบ (ไป Recycle Bin) + เขียน log |
| 6 | **📝 สรุปผล** | แสดงสิ่งที่เปลี่ยนไป + maintenance tips |

---

## 🛡️ Safety First

File Wizard ให้ความสำคัญกับความปลอดภัยของข้อมูลของคุณเป็นอันดับหนึ่ง:

| หลักการ | รายละเอียด |
|---------|-----------|
| ♻️ **ทุกการลบไป Recycle Bin** | ใช้ `[Microsoft.VisualBasic.FileIO.FileSystem]::DeleteFile()` — ไม่มี `Remove-Item` โดยตรง |
| 👁️ **Preview เสมอ** | ใช้ `-WhatIf` ก่อน execute จริงทุกครั้ง |
| ✅ **Confirm ทุกครั้ง** | user ต้องพิมพ์ `yes` ก่อนทำ destructive action ใดๆ |
| 📝 **Log ทุก operation** | เขียน log ไฟล์ไว้ใน target directory สำหรับ undo reference |
| 🚫 **ห้าม touch system folders** | ถ้าเจอ system folder → หยุดและถาม user ทันที |
| ❓ **เจอ unexpected — หยุดถาม** | permission denied, file ซ้ำชื่อ, ไฟล์体积ใหญ่ → หยุดก่อน |

---

## 📦 สิ่งที่ได้จาก skill นี้

| หมวด | ไฟล์ | ใช้ตอนไหน |
|------|------|----------|
| 📖 **PowerShell Commands** | `reference/powershell-commands.md` | อ้างอิงคำสั่ง PowerShell จัดการไฟล์ |
| 🛡️ **Safety Rules** | `reference/safety-rules.md` | กฎความปลอดภัย + ข้อยกเว้น |
| 📝 **Test Prompts** | `evals/evals.json` | ตัวอย่าง prompt ทดสอบ (สำหรับ dev) |

---

## 📂 โครงสร้างโฟลเดอร์

```
file-wizard/
│
├── SKILL.md              ← 🧠 Entry point หลัก — ให้ AI agent อ่าน
│
├── reference/            ← 📚 ข้อมูลอ้างอิง
│   ├── powershell-commands.md   ← คำสั่ง PowerShell สำหรับจัดการไฟล์
│   └── safety-rules.md          ← กฎความปลอดภัย + การจัดการข้อผิดพลาด
│
└── evals/                ← 🧪 Test prompts (สำหรับพัฒนา)
    └── evals.json              ← 4 test cases: จัด Downloads · หาไฟล์ซ้ำ · rename · cleanup
```

---

## 🔗 ระบบที่เกี่ยวข้อง

| Skill | ใช้คู่กันยังไง |
|-------|--------------|
| 🎌 **[ikigai](https://github.com/hartkitsak/ikigai)** | จัดระเบียบชีวิตไปพร้อมกับจัดระเบียบไฟล์ — ikigai ช่วยหาเป้าหมาย, File Wizard จัดสภาพแวดล้อม |
| ⚡ **[zecision](https://github.com/hartkitsak/zecision)** | ลังเลว่าจะลบไฟล์ทิ้งหรือเก็บไว้? zecision ช่วยตัดสินใจ, File Wizard จัดการให้ |
| 🎬 **[mrbeginner](https://github.com/hartkitsak/mrbeginner)** | ทำ YouTube แล้วไฟล์ raw footage รก? mrbeginner ช่วยวาง production pipeline, File Wizard จัดระเบียบ media files |

---

## 📥 ติดตั้ง

```powershell
# วาง skill ไว้ที่ ~/.claude/skills/ (สำหรับ Claude Code)
git clone https://github.com/hartkitsak/file-wizard.git "$env:USERPROFILE\.claude\skills\file-wizard"
```

หรือ clone ที่ไหนก็ได้ แล้วเพิ่ม path ใน config (สำหรับ opencode / tools อื่นๆ):

```json
{
  "skills": [
    "path/to/file-wizard"
  ]
}
```

**Requirements:** Windows 10/11, PowerShell 7+, Claude Code / opencode

---

## 📄 License

MIT — free to use, modify, and share.

Built by [hartkitsak](https://github.com/hartkitsak)
