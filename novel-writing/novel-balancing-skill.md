---
name: novel-balancing
description: สกิลกลางสำหรับเกลี่ยความยาวตอนนิยายให้สม่ำเสมอ (default 3,000–4,000 อักขระ ไม่รวมเว้นวรรค) — ใช้งานได้กับนิยายเรื่องใดก็ได้ที่มีโฟลเดอร์ตอน (03-episodes) ไม่ผูกกับเรื่องใดเรื่องนึง ใช้วิธีเกลาขอบเขต (Boundary Smoothing) + เคารพ Episode Bucket (ห้ามย้ายเหตุการณ์สำคัญข้ามตอน) + แนะนำ "เติม/ตัด" แทนการสลับย่อหน้าข้ามตอน ทำงานก่อนคอมไพล์ คู่กับ novel-structure / novel-episode / novel-recheck รองรับความยาวแบ่งตามประเภทตอน (เปิดเรื่อง/ไฮไลท์/จบอาร์ค/คั่นพิเศษ ตาม episode-template)
---

# Skill: เกลี่ยความยาวตอนนิยาย (Novel Episode Balancing) — สกิลกลาง ใช้งานได้กับนิยายเรื่องใดก็ได้

## 1. บทบาท (Role)
คุณคือ **ผู้เกลี่ยความยาวระดับชุดตอน (Length Balancer)** — ทำงานหลังจากเขียนตอนไปแล้วหลายตอน เพื่อให้ความยาวแต่ละตอนสม่ำเสมอ ใกล้เป้าหมายโปรเจกต์ (default 3,000–4,000 อักขระ ไม่รวม space)
- **กลาง (generic):** ไม่ผูกกับเรื่องใดเรื่องนึง ใช้งานได้กับโปรเจกต์นิยายใดก็ได้ที่มี `03-episodes/` (และแนะนำให้มี `04-continuity/` สำหรับอัปเดต Canon Facts)
- ไม่ใช่ผู้เขียนตอน (เขียนตอนเป็นหน้าที่ `novel-episode`) และไม่ใช่ผู้ตรวจ (ตรวจเป็นหน้าที่ `novel-recheck`) — สกิลนี้แค่ "เกลี่ยความยาว + ขัดเกลารอยต่อ" ก่อนคอมไพล์

## 2. หลักการสำคัญ (ต้องทำตาม)
- **Continuity First:** ห้ามทำลายความต่อเนื่อง — การเกลี่ยต้องไม่ย้าย "เหตุการณ์ที่ต้องเกิดในตอน X" (จาก Episode Bucket / macro-arcs) ไปตอนอื่น
- **เคารพ Episode Bucket:** ย่อหน้าที่อยู่ในตอนที่ล็อกเหตุการณ์สำคัญ = **ล็อกอยู่กับตอนนั้น ห้ามขยับ**
- **เติม/ตัด ดีกว่าสลับข้ามตอน (แนวทางหลัก):**
  - ตอนสั้น → **เติม** เนื้อหาเชื่อมโยง (slice-of-life / อารมณ์ตัวละคร / บรรยากาศ) ตามที่ `novel-structure` แนะนำ ไม่ใช่แย่งย่อหน้าจากตอนอื่น
  - ตอนยาว → **ตัดทอน** ส่วนซ้ำซาก / adjective ซ้อน / บรรยายเกินจำเป็น (ตรวจกับกฎภาษาของ `novel-episode`)
- **เฉพาะเมื่อจำเป็นจริงๆ** จึง redistribute ย่อหน้า "อิสระ" (free paragraph — ไม่ล็อก ไม่ใช่จุดคุมพลอต) ระหว่างตอนที่ **ติดกันเท่านั้น** (ไม่ข้ามไกล)
- **รักษาฟอร์แมต Canon Facts** ให้ตรงกับที่ `novel-episode` ใช้ (ดู ข้อ 6)

## 2.5 ความยาวแบ่งตามประเภทตอน (Per-Type Target)
โปรเจกต์ที่ใช้ `episode-template.md` กำหนดความยาวต่างกันตามประเภทตอน (ดูตาราง "ตอนพิเศษ" ใน template) สกิลนี้รองรับการเกลาแบบแยกประเภท แทนการใช้ค่าเฉลี่ยเดียว:

| ประเภทตอน | เรนจ์ความยาว (อักขระ ไม่รวม space) |
|-----------|--------------------------------------|
| ธรรมดา (มาตรฐาน) | 3,000–4,000 |
| เปิดเรื่อง | 3,500–4,500 |
| ไฮไลท์/ศึกใหญ่ | 4,000–5,500 |
| จบอาร์ค | 3,500–4,500 |
| คั่น/พิเศษ | 1,500–2,500 |

**วิธีระบุประเภทตอน (เลือกอย่างใดอย่างหนึ่ง):**
1. ไฟล์ `episode-types.json` ที่ root โปรเจกต์: `{"1": "เปิดเรื่อง", "23": "ไฮไลท์", "50": "จบอาร์ค", ...}`
2. บรรทัดทำเครื่องหมายในไฟล์ตอน: `ประเภทตอน: ไฮไลท์` (สกิลจะจับคู่กับคำในตาราง)
3. ไม่ระบุ → ถือเป็น "ธรรมดา" (มาตรฐาน)

> หมายเหตุ: ตารางใน template เดิมใช้คำว่า "คำ" แต่ในทางปฏิบัติโปรเจกต์นี้วัดเป็น "อักขระ" (ดู Editor Notes/Canon Facts) สกิลนี้จึงตีความเรนจ์ข้างต้นเป็น **อักขระ** ให้สอดคล้องกัน

## 3. ขั้นตอนการทำงาน (Workflow)
### ขั้นที่ 1: วัดความยาว (Measure)
- นับอักขระแต่ละตอน (ไม่รวม space / newline) จาก `03-episodes/episode-*.md`
- ตรวจประเภทตอนของแต่ละตอน (ดู ข้อ 2.5) แล้วเปรียบเทียบกับเรนจ์ของประเภทนั้น — ไม่ใช่ค่าเฉลี่ยเดียว
- ทำเครื่องหมายตอนที่ความยาวหลุดเรนจ์ของประเภทตนเอง

### ขั้นที่ 2: โหลด locks (Episode Bucket)
- อ่าน `06-outline/macro-arcs.md` + Episode Bucket ของแต่ละตอน (ถ้ามี)
- มาร์คย่อหน้าที่ belongs to "เหตุการณ์ที่ต้องเกิด" → **LOCKED** (ห้ามขยับข้ามตอน)

### ขั้นที่ 3: วางแผนเกลา (Plan)
- ตอนสั้น: วางแผน "เติม" อะไร (อ้างอิงโครง ห้ามคิดโลกเองนอกโครง)
- ตอนยาว: หาจุดตัดทอน (stutter / adjective ซ้อน / บรรยายซ้ำ)
- เฉพาะ free paragraph ที่ติดกัน → เสนอ redistribute เล็กน้อย

### ขั้นที่ 4: เกลารอยต่อ (Boundary Smoothing)
หลังปรับตอน ตรวจจุดหัว/ท้ายตอน:
1. ประโยคขาด → ทำให้สมบูรณ์ปิดท้ายตอน
2. บทสนทนาคู่ที่ถูกแยก → รวมหรือย้ายไปตอนถัดไป
3. สรรพนามชี้ ("พวกมัน" / "เขา") ขึ้นต้นตอน → เปลี่ยนเป็นชื่อตัวละคร
4. Hook / Cliffhanger ท้ายตอนให้น่าติดตาม

### ขั้นที่ 5: อัปเดต Metadata (ต้องตรงฟอร์แมต novel-episode)
- ตั้งชื่อตอนใหม่ (สะท้อนพล็อต)
- Editor Notes สรุปเจตนา + นับอักขระใหม่
- **Canon Facts** ฟอร์แมตเดียวกับ `novel-episode` (ข้อ 6)
- อัปเดต `04-continuity/continuity-log.md` ให้สอดคล้อง

### ขั้นที่ 6: คอมไพล์ (ถ้าโปรเจกต์ใช้)
- รันสคริปต์คอมไพล์เป็น `.docx` (ถ้ามี) — font TH Sarabun New

## 4. สคริปต์สนับสนุน (Helper Scripts) — path กลาง ปรับได้
สคริปต์อ่าน path จาก (1) argument แรก (2) env `BALANCE_BASE` (3) โฟลเดอร์ปัจจุบัน — ไม่มี path ฝังตายตัว

### 4.1 วัดความยาวต่อตอน (measure.py)
```python
import os, sys, glob, re, json

base = sys.argv[1] if len(sys.argv) > 1 else os.environ.get("BALANCE_BASE", os.getcwd())
episodes_dir = os.path.join(base, "03-episodes")

# เรนจ์ความยาวต่อประเภทตอน (หน่วย: อักขระ ไม่รวม space) — ต้องตรงกับ episode-template.md
TYPE_RANGES = {
    "ธรรมดา":   (3000, 4000),
    "เปิดเรื่อง": (3500, 4500),
    "ไฮไลท์":   (4000, 5500),
    "จบอาร์ค":   (3500, 4500),
    "คั่นพิเศษ": (1500, 2500),
}

# โหลดแผนที่ประเภทจาก episode-types.json (ถ้ามี): {"1": "เปิดเรื่อง", "23": "ไฮไลท์", ...}
type_map = {}
jt = os.path.join(base, "episode-types.json")
if os.path.exists(jt):
    try:
        type_map = {str(k): v for k, v in json.load(open(jt, encoding="utf-8")).items()}
    except Exception as e:
        print(f"คำเตือน: อ่าน episode-types.json ไม่ได้ ({e})")

def detect_type(num, text):
    if str(num) in type_map:
        return type_map[str(num)]
    m = re.search(r"ประเภทตอน[:\s]+([^\n|]+)", text)
    if m:
        tok = m.group(1).strip().strip("|").strip()
        for key in TYPE_RANGES:
            if key in tok:
                return key
    return "ธรรมดา"

files = sorted(
    f for f in glob.glob(os.path.join(episodes_dir, "episode-*.md"))
    if re.match(r"^episode-\d+\.md$", os.path.basename(f))
)
if not files:
    print(f"ไม่พบตอนใน {episodes_dir}")
    sys.exit(1)

print("เรนจ์ความยาวต่อประเภท (อักขระ ไม่รวม space):")
for k, (lo, hi) in TYPE_RANGES.items():
    print(f"  {k}: {lo:,}-{hi:,}")
print("-" * 50)

problems = []
for f in files:
    fn = os.path.basename(f)
    mm = re.match(r"episode-(\d+)\.md", fn)
    num = int(mm.group(1)) if mm else 0
    text = open(f, encoding="utf-8").read()
    story = text.split("---")[0]          # เอาเฉพาะเนื้อเรื่องหลัก
    n = len(story.replace(" ", "").replace("\n", "").replace("\r", "").replace("\t", ""))
    t = detect_type(num, text)
    lo, hi = TYPE_RANGES.get(t, TYPE_RANGES["ธรรมดา"])
    if n < lo:
        problems.append((fn, t, n, f"สั้นกว่าเป้าหมาย {lo:,}-{hi:,}"))
    elif n > hi:
        problems.append((fn, t, n, f"ยาวกว่าเป้าหมาย {lo:,}-{hi:,}"))
    print(f"{fn} [ประเภท {t}] : {n:,} อักขระ -> {'OK' if lo <= n <= hi else 'ผิดเรนจ์'}")

print("-" * 50)
print(f"ตอนที่ความยาวผิดเรนจ์ของประเภทตนเอง: {len(problems)} ตอน")
for fn, t, n, s in problems:
    print(f"  {fn} | ประเภท={t} | {n:,} อักขระ | {s}")
```

### 4.2 (ยกเลิก redistribute แบบอัตโนมัติ)
> การย้ายย่อหน้าข้ามตอนด้วยสคริปต์มีความเสี่ยงทำลาย Episode Bucket locks
> — สกิลนี้เองก็ระบุว่า "เติม/ตัด ดีกว่าสลับข้ามตอน" (ข้อ 2)
> ให้ใช้วิธี "เติม/ตัด" เป็นหลักเสมอ
> หากจำเป็นต้อง redistribute → **ทำด้วยมือ ตรวจ locks ก่อนทุกครั้ง**

### 4.3 คอมไพล์ตอนเป็น .docx (compile.py)
> **ต้องติดตั้ง dependency ก่อนใช้งาน:** `pip install python-docx`

สคริปต์อ่าน path จาก (1) argument แรก (2) env `COMPILE_BASE` (3) โฟลเดอร์ปัจจุบัน
```python
"""compile.py — คอมไพล์ตอนนิยายเป็น .docx (ต้องติดตั้ง python-docx)"""
import os, sys, glob, re
from docx import Document
from docx.shared import Pt
from docx.enum.text import WD_ALIGN_PARAGRAPH

base = sys.argv[1] if len(sys.argv) > 1 else os.environ.get("COMPILE_BASE", os.getcwd())
episodes_dir = os.path.join(base, "03-episodes")
out_file = os.path.join(base, "compiled", "novel.docx")
os.makedirs(os.path.dirname(out_file), exist_ok=True)

doc = Document()
style = doc.styles["Normal"]
style.font.name = "TH Sarabun New"
style.font.size = Pt(16)

files = sorted(
    f for f in glob.glob(os.path.join(episodes_dir, "episode-*.md"))
    if re.match(r"^episode-\d+\.md$", os.path.basename(f))
)
if not files:
    print(f"ไม่พบตอนใน {episodes_dir}")
    sys.exit(1)

for f in files:
    text = open(f, encoding="utf-8").read()
    # ตัด Canon Facts / Editor Notes ออก (หลัง --- แรก)
    story = text.split("---")[0] if "---" in text else text

    lines = story.strip().split("\n")
    for line in lines:
        line = line.strip()
        if not line:
            continue
        if line.startswith("# "):
            doc.add_heading(line.lstrip("# "), level=1)
        elif line.startswith("## "):
            doc.add_heading(line.lstrip("## "), level=2)
        else:
            doc.add_paragraph(line)

    doc.add_page_break()

doc.save(out_file)
print(f"คอมไพล์ {len(files)} ตอน → {out_file}")
```


## 5. ตัวอย่างการเรียกใช้ (Invocation)
- "เกลี่ยความยาวตอน 1–20 ให้เท่ากัน" → ข้อ 3.1 วัด → ข้อ 3.2 โหลด locks → ข้อ 3.3 วางแผน → ข้อ 4 เกลา → ข้อ 5 อัปเดต
- "ตอน 5 สั้นไป ช่วยเติม" → แนะนำเติมตามโครง (ไม่แย่งตอนอื่น)
- "ตรวจก่อน compile" → รัน measure.py + เช็ค locks กับ macro-arcs

## 6. ฟอร์แมต Canon Facts (ต้องตรงกับ novel-episode)
```
[Canon Facts ตอนที่ N]
- ตัวละครที่ปรากฏ: [ชื่อ] ([สถานะใหม่ ถ้ามี])
- สถานที่: [ชื่อสถานที่ปัจจุบัน]
- เวลาในเรื่อง: [วัน/เวลา]
- ปมที่คลาย: [รายการ]
- ปมที่ปูใหม่/ยังค้าง: [รายการ]
- ของ/ข้อมูลใหม่ที่แนะนำ: [รายการ]
- กฎ/ระบบที่ใช้/ยืนยันในตอนนี้: [รายการ]
```

---

*สกิลนี้คือ "ผู้เกลี่ยความยาว" — กลาง ใช้งานได้กับนิยายเรื่องใดก็ได้ โครงจาก novel-structure การเขียนจาก novel-episode การตรวจจาก novel-recheck*
