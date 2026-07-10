---
name: novel-recheck
description: สกิลสำหรับตรวจสอบหลายตอนพร้อมกัน (Batch Recheck) หลังจากเขียนผ่านไปแล้ว — หาจุดผิดพลาด คำผิด ความเชื่อมโยงผิดพลาด (continuity break) stutter ย่อหน้าซ้ำ คำซ้ำข้ามตอน ภาษาผิดกฎ (อิโมจิ/อังกฤษนอก glossary) โดยรันเป็นระยะ (แนะนำทุก 10 ตอน / ทุกจบอาร์ค / ทุก 100 ตอน) ใช้คู่กับ novel-episode
---

# Skill: ตรวจสอบหลายตอนพร้อมกัน (Batch Recheck)

## 1. บทบาท (Role)
คุณคือ **ผู้ตรวจคุณภาพระดับชุดตอน (Batch QA)** — ทำงานหลังจากเขียนตอนไปแล้วหลายตอน
ไม่ใช่ผู้เขียนตอน (การเขียนเป็นหน้าที่ของ `novel-episode`)

## 2. เมื่อไรให้รัน (Cadence) — คำแนะนำของผู้ช่วย
- **Light Recheck ทุก 10 ตอน**: สแกนเฉพาะชุด 10 ตอนล่าสุด (รวดเร็ว) หา stutter / คำผิด / ย่อหน้าซ้ำ / ภาษาผิดกฎ
- **Deep Recheck ทุกจบอาร์ค** (~20–25 ตอน): Continuity Diff เต็มรูปแบบ + Structure Alignment ของอาร์คนั้น + สร้าง Continuity Snapshot (§3.5)
- **Full Recheck ทุก 100 ตอน / ทุกจบ Volume**: สำหรับ 1000+ ตรวจทั้งเล่มเพื่อมั่นใจว่าเชื่อมโยงครบ + สร้าง Continuity Snapshot (§3.5)
- **ก่อน compile / publish**: สแกนทั้งหมดเสมอ
- ⚠️ สำหรับ 1000+ ตอน **ห้าม recheck ทั้งหมดทุกครั้ง** (ช้าเกินไป) → ใช้ incremental (เฉพาะชุดล่าสุด) + milestone ใหญ่ (ทุก 100 / ทุกจบ Volume)

## 3. ขั้นตอนการตรวจรวม (Batch Recheck Procedure)
1. โหลด `04-continuity/continuity-log.md` + `06-outline/macro-arcs.md` + `07-notes/glossary.md`
2. รันสคริปต์สแกน (ข้อ 6) หา:
   - stutter (คำซ้ำ 3 ครั้งติด)
   - ย่อหน้าซ้ำ (duplicated paragraph)
   - คำซ้ำข้ามตอน (vocabulary monotony)
   - ความยาวผิดเกณฑ์ (แบ่งตามประเภทตอน)
   - อิโมจิ
   - คำภาษาอังกฤษนอก glossary
   - ตอนที่ไม่มี Canon Facts ท้ายตอน
3. **Continuity Diff**: เทียบ Canon Facts ตอนต่อตอน —
   - ตัวละครที่ตาย/จากไป ห้ามโผล่กลับโดยไม่มีเหตุผล
   - สถานที่/เวลาตรงกับ Timeline
   - ปมค้างต้องสืบเนื่องกัน (ไม่หายไปเงียบๆ)
4. **Structure Alignment รวม**: ตอนทั้งหมดตรง `macro-arcs.md` / Episode Bucket หรือไม่
5. รวบรวม defect เป็นรายงาน (ข้อ 4)

### 3.5 สร้าง Continuity Snapshot (ทุก 25 ตอน + ตอนรัน Deep/Full Recheck)
หลังรัน Deep Recheck หรือ Full Recheck หรือเมื่อครบทุก 25 ตอน สร้างไฟล์ `04-continuity/snapshot-ep{N}.md`:
- ตัวละครที่ยังมีชีวิต + สถานะ ณ ตอนล่าสุด
- ปมค้างที่ยังไม่ปิด (พร้อมระบุตอนที่ปูไว้)
- กฎ/ระบบที่ยืนยันแล้ว
- Timeline สรุป (เวลาในเรื่อง ณ ตอนล่าสุด)
- ของ/ข้อมูลสำคัญที่ตัวละครถือ
- ขนาดไม่เกิน 200 บรรทัด

> Snapshot นี้ให้ `novel-episode` ใช้แทน continuity-log เต็มเมื่อมี 25+ ตอน (ดู novel-episode §3.5)
> สำหรับโปรเจกต์ที่ยังไม่ถึง 25 ตอน → ยังไม่ต้องสร้าง snapshot (novel-episode จะโหลด continuity-log เต็มแทน)

### 3.6 Voice Consistency Check (ตรวจเสียงตัวละครข้ามตอน)
เมื่อรัน Deep Recheck หรือ Full Recheck **และโปรเจกต์มี Voice Sheet ใน `02-characters/`**:

1. รันสคริปต์ดึงบทพูด (ข้อ 7) เพื่อแยกบทพูดตามตัวละคร
2. เปรียบเทียบกับ Voice Sheet ใน `02-characters/`:
   - ระดับภาษา (หยาบ/สุภาพ/กลางๆ) คงที่หรือไม่
   - คำติดปาก / สำนวนเฉพาะตัว ยังใช้อยู่หรือหายไป
   - ถ้าเสียงเปลี่ยน → ต้องมีเหตุผลในเนื้อเรื่อง (character development)
3. ประเภท defect: `VOICE-DRIFT`
   — format: `ตอน X / VOICE-DRIFT / [ชื่อตัวละคร]: เสียงเปลี่ยนจาก [แบบเดิม] เป็น [แบบใหม่] โดยไม่มีเหตุผล`

> ⚠️ ถ้าโปรเจกต์ไม่มี Voice Sheet ใน `02-characters/` → ข้ามข้อนี้
> การวิเคราะห์เสียงตัวละครเป็นหน้าที่ของ AI — สคริปต์ (ข้อ 7) แค่ดึงบทพูดออกมาเตรียมให้

## 4. รูปแบบรายงาน (Report)
ระบุต่อจุด: `ตอน / ประเภท / รายละเอียด / ตำแหน่ง (บรรทัด)`
ประเภท defect:
`STUTTER` · `DUP-PARA` · `TYPO` · `MONOTONY` · `LENGTH` · `EMOJI` · `ENGLISH` · `MISSING-CANON` · `CONTINUITY-BREAK` · `STRUCTURE-MISMATCH` · `VOICE-DRIFT`

> หมายเหตุ: `CONTINUITY-BREAK`, `STRUCTURE-MISMATCH`, `VOICE-DRIFT`, `TYPO` ต้องตรวจด้วย AI (เปรียบเทียบ Canon Facts / macro-arcs / Voice Sheet) — สคริปต์ทำได้แค่ flag ข้อมูลที่ขาด (`MISSING-CANON`) และดึงบทพูดเตรียมให้ (`VOICE-DRIFT`)

## 5. ตัวอย่างการเรียกใช้ (Invocation)
- `"ตรวจตอน 1–200 ทั้งหมด"` → รัน ข้อ 3 เต็มรูปแบบ + สร้าง Snapshot
- `"ตรวจตอน 191–200 ล่าสุด"` → Light Recheck ชุดล่าสุด
- `"ตรวจก่อน compile เล่ม 1"` → Full Recheck + สร้าง Snapshot

## 6. สคริปต์สแกนรวม (scan.py — ตรวจจับ defect อัตโนมัติ)
สคริปต์อ่าน path จาก (1) argument แรก (2) env `RECHECK_BASE` (3) โฟลเดอร์ปัจจุบัน — ไม่มี path ฝังตายตัว
```python
import os, sys, glob, re, json
from collections import Counter

base = sys.argv[1] if len(sys.argv) > 1 else os.environ.get("RECHECK_BASE", os.getcwd())
episodes_dir = os.path.join(base, "03-episodes")
glossary_file = os.path.join(base, "07-notes", "glossary.md")

# --- เรนจ์ความยาวต่อประเภทตอน (หน่วย: อักขระ ไม่รวม space) ---
TYPE_RANGES = {
    "ธรรมดา":   (3000, 4000),
    "เปิดเรื่อง": (3500, 4500),
    "ไฮไลท์":   (4000, 5500),
    "จบอาร์ค":   (3500, 4500),
    "คั่นพิเศษ": (1500, 2500),
}

# โหลดแผนที่ประเภทจาก episode-types.json (ถ้ามี)
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

# --- โหลด glossary (คำที่อนุญาตให้เป็นอังกฤษ) ---
glossary = set()
if os.path.exists(glossary_file):
    for line in open(glossary_file, encoding="utf-8"):
        for w in re.findall(r'[A-Za-z]{2,}', line):
            glossary.add(w.lower())

# --- Stopwords ภาษาไทย (กรองออกจาก MONOTONY) ---
STOPWORDS = {
    "และ", "การ", "ที่", "ของ", "ใน", "จะ", "ได้", "ไม่", "เป็น", "มี",
    "ให้", "กับ", "ว่า", "แล้ว", "ก็", "ไป", "มา", "อยู่", "นี้", "นั้น",
    "คือ", "แต่", "จาก", "หรือ", "ด้วย", "เมื่อ", "ถ้า", "ยัง", "อีก",
    "ทำ", "คน", "เขา", "มัน", "เรา", "ตัว", "ขึ้น", "ลง", "ออก", "เข้า",
}

files = sorted(
    f for f in glob.glob(os.path.join(episodes_dir, "episode-*.md"))
    if re.match(r"^episode-\d+\.md$", os.path.basename(f))
)
if not files:
    print(f"ไม่พบตอนใน {episodes_dir}")
    sys.exit(1)

emoji_re = re.compile(r'[\U0001F000-\U0001FAFF\U00002600-\U000027BF]')
word_freq = Counter()  # สะสมคำข้ามตอนสำหรับ MONOTONY

for f in files:
    text = open(f, encoding="utf-8").read()
    paras = [p.strip() for p in text.split("\n") if p.strip()]
    name = os.path.basename(f)
    mm = re.match(r"episode-(\d+)\.md", name)
    num = int(mm.group(1)) if mm else 0

    # 1. ย่อหน้าซ้ำ (DUP-PARA)
    for i in range(len(paras) - 1):
        if paras[i] == paras[i + 1] and len(paras[i]) > 25:
            print(f"[DUP-PARA] {name}: บรรทัด {i+1}")

    # 2. stutter (STUTTER)
    words = text.split()
    i = 0
    while i < len(words):
        j = i
        while j < len(words) and words[j] == words[i]:
            j += 1
        if j - i >= 3:
            print(f"[STUTTER] {name}: {words[i]!r} x{j-i}")
        i = j

    # 3. อิโมจิ (EMOJI)
    if emoji_re.search(text):
        print(f"[EMOJI] {name}")

    # 4. ภาษาอังกฤษนอก glossary (ENGLISH)
    for m in re.findall(r'[A-Za-z]{2,}', text):
        if m.lower() not in glossary:
            print(f"[ENGLISH] {name}: {m}")
            break

    # 5. ความยาวผิดเกณฑ์ — ใช้เรนจ์แบ่งตามประเภทตอน (LENGTH)
    story = text.split("---")[0]
    n = len(story.replace(" ", "").replace("\n", "").replace("\r", "").replace("\t", ""))
    t = detect_type(num, text)
    lo, hi = TYPE_RANGES.get(t, TYPE_RANGES["ธรรมดา"])
    if n < lo:
        print(f"[LENGTH] {name} [ประเภท {t}]: {n:,} อักขระ (ต่ำกว่าเป้าหมาย {lo:,}-{hi:,})")
    elif n > hi:
        print(f"[LENGTH] {name} [ประเภท {t}]: {n:,} อักขระ (เกินเป้าหมาย {lo:,}-{hi:,})")

    # 6. ตอนที่ไม่มี Canon Facts (MISSING-CANON)
    if "[Canon Facts" not in text:
        print(f"[MISSING-CANON] {name}: ไม่พบ [Canon Facts] ท้ายตอน")

    # 7. สะสมคำสำหรับ MONOTONY (กรอง stopword)
    for w in re.findall(r'[\u0E00-\u0E7F]{3,}', text):
        if w not in STOPWORDS:
            word_freq[w] += 1

# --- MONOTONY: รายงานคำที่ใช้ซ้ำมากผิดปกติข้ามตอน ---
# threshold = ค่าเฉลี่ย > 1.5 ครั้งต่อตอน (ปรับได้)
mono_threshold = max(15, int(len(files) * 1.5))
print(f"\n--- MONOTONY (คำที่ปรากฏ > {mono_threshold} ครั้ง ใน {len(files)} ตอน) ---")
for w, c in word_freq.most_common(30):
    if c >= mono_threshold:
        print(f"[MONOTONY] \"{w}\": {c} ครั้ง ({c/len(files):.1f} ครั้ง/ตอน)")

print(f"\nสแกนเสร็จ {len(files)} ตอน")
```

> **หมายเหตุ**: `CONTINUITY-BREAK`, `STRUCTURE-MISMATCH`, `VOICE-DRIFT`, `TYPO` และ adjectives ซ้อนเกิน 2 ชั้น ต้องตรวจด้วย AI — สคริปต์ตรวจจับรูปแบบเหล่านี้ได้ไม่แม่นยำพอ (โดยเฉพาะ adjective ซ้อนแบบไม่มีคำเชื่อมในภาษาไทย)

## 7. สคริปต์ดึงบทพูดตามตัวละคร (dialogue-extract.py — เตรียมข้อมูลให้ AI ตรวจ Voice Consistency)
สคริปต์อ่าน path จาก (1) argument แรก (2) env `RECHECK_BASE` (3) โฟลเดอร์ปัจจุบัน
```python
import os, sys, glob, re

base = sys.argv[1] if len(sys.argv) > 1 else os.environ.get("RECHECK_BASE", os.getcwd())
episodes_dir = os.path.join(base, "03-episodes")
characters_dir = os.path.join(base, "02-characters")

# โหลดรายชื่อตัวละครจากไฟล์ใน 02-characters/
char_names = []
if os.path.isdir(characters_dir):
    for cf in glob.glob(os.path.join(characters_dir, "*.md")):
        text = open(cf, encoding="utf-8").read()
        # จับชื่อจากหัวข้อแรก (# ชื่อ) หรือจากชื่อไฟล์
        m = re.search(r"^#\s+(.+)", text, re.MULTILINE)
        if m:
            char_names.append(m.group(1).strip())
        else:
            char_names.append(os.path.splitext(os.path.basename(cf))[0])

if not char_names:
    print("ไม่พบตัวละครใน 02-characters/ — ข้าม Voice Consistency")
    sys.exit(0)

print(f"ตัวละครที่ตรวจ: {', '.join(char_names)}")
print("=" * 50)

# regex จับบทพูด: "..." หรือ «...» หรือ 「...」
dialogue_re = re.compile(r'["\u201C\u00AB\u300C](.+?)["\u201D\u00BB\u300D]')

files = sorted(
    f for f in glob.glob(os.path.join(episodes_dir, "episode-*.md"))
    if re.match(r"^episode-\d+\.md$", os.path.basename(f))
)

for f in files:
    text = open(f, encoding="utf-8").read()
    name = os.path.basename(f)
    lines = text.split("\n")

    for i, line in enumerate(lines, 1):
        dialogues = dialogue_re.findall(line)
        if not dialogues:
            continue
        # หาตัวละครที่อยู่ใกล้บทพูด (ในบรรทัดเดียวกันหรือ 2 บรรทัดก่อน)
        context = "\n".join(lines[max(0,i-3):i])
        for char in char_names:
            if char in context or char in line:
                for d in dialogues:
                    print(f"[{name}:L{i}] {char}: \"{d}\"")
                break

print(f"\nดึงบทพูดเสร็จ {len(files)} ตอน — ส่งผลลัพธ์ให้ AI ตรวจ Voice Consistency")
```

---

*สกิลนี้คือ "ผู้ตรวจระดับชุดตอน" — โครงจาก novel-structure การเขียนจาก novel-episode*
