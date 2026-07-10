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
- **Deep Recheck ทุกจบอาร์ค** (~20–25 ตอน): Continuity Diff เต็มรูปแบบ + Structure Alignment ของอาร์คนั้น
- **Full Recheck ทุก 100 ตอน / ทุกจบ Volume**: สำหรับ 1000+ ตรวจทั้งเล่มเพื่อมั่นใจว่าเชื่อมโยงครบ
- **ก่อน compile / publish**: สแกนทั้งหมดเสมอ
- ⚠️ สำหรับ 1000+ ตอน **ห้าม recheck ทั้งหมดทุกครั้ง** (ช้าเกินไป) → ใช้ incremental (เฉพาะชุดล่าสุด) + milestone ใหญ่ (ทุก 100 / ทุกจบ Volume)

## 3. ขั้นตอนการตรวจรวม (Batch Recheck Procedure)
1. โหลด `04-continuity/continuity-log.md` + `06-outline/macro-arcs.md` + `07-notes/glossary.md`
2. รันสคริปต์สแกน (ข้อ 6) หา:
   - stutter (คำซ้ำ 3 ครั้งติด)
   - ย่อหน้าซ้ำ (duplicated paragraph)
   - คำซ้ำข้ามตอน (vocabulary monotony)
   - ความยาวผิดเกณฑ์
   - อิโมจิ
   - คำภาษาอังกฤษนอก glossary
3. **Continuity Diff**: เทียบ Canon Facts ตอนต่อตอน —
   - ตัวละครที่ตาย/จากไป ห้ามโผล่กลับโดยไม่มีเหตุผล
   - สถานที่/เวลาตรงกับ Timeline
   - ปมค้างต้องสืบเนื่องกัน (ไม่หายไปเงียบๆ)
4. **Structure Alignment รวม**: ตอนทั้งหมดตรง `macro-arcs.md` / Episode Bucket หรือไม่
5. รวบรวม defect เป็นรายงาน (ข้อ 4)

## 4. รูปแบบรายงาน (Report)
ระบุต่อจุด: `ตอน / ประเภท / รายละเอียด / ตำแหน่ง (บรรทัด)`
ประเภท defect:
`STUTTER` · `DUP-PARA` · `TYPO` · `MONOTONY` · `LENGTH` · `EMOJI` · `ENGLISH` · `CONTINUITY-BREAK` · `STRUCTURE-MISMATCH`

## 5. ตัวอย่างการเรียกใช้ (Invocation)
- `"ตรวจตอน 1–200 ทั้งหมด"` → รัน ข้อ 3 เต็มรูปแบบ
- `"ตรวจตอน 191–200 ล่าสุด"` → Light Recheck ชุดล่าสุด
- `"ตรวจก่อน compile เล่ม 1"` → Full Recheck

## 6. สคริปต์สแกนรวม (ให้ผู้ใช้รันในเครื่อง — ตรวจจับ defect อัตโนมัติ)
```python
import os, glob, re

base = "03-episodes"        # ปรับเป็น path จริงของโฟลเดอร์ตอน
glossary_file = "07-notes/glossary.md"  # ปรับเป็น path จริง

# โหลด glossary (คำที่อนุญาตให้เป็นอังกฤษ)
glossary = set()
if os.path.exists(glossary_file):
    for line in open(glossary_file, encoding="utf-8"):
        for w in re.findall(r'[A-Za-z]{2,}', line):
            glossary.add(w.lower())

files = sorted(glob.glob(os.path.join(base, "episode-*.md")))
emoji_re = re.compile(r'[\U0001F000-\U0001FAFF\U00002600-\U000027BF]')

for f in files:
    text = open(f, encoding="utf-8").read()
    paras = [p.strip() for p in text.split("\n") if p.strip()]
    name = os.path.basename(f)

    # 1. ย่อหน้าซ้ำ
    for i in range(len(paras) - 1):
        if paras[i] == paras[i + 1] and len(paras[i]) > 25:
            print(f"[DUP-PARA] {name}: บรรทัด {i+1}")

    # 2. stutter
    words = text.split()
    i = 0
    while i < len(words):
        j = i
        while j < len(words) and words[j] == words[i]:
            j += 1
        if j - i >= 3:
            print(f"[STUTTER] {name}: {words[i]!r} x{j-i}")
        i = j

    # 3. อิโมจิ
    if emoji_re.search(text):
        print(f"[EMOJI] {name}")

    # 4. ภาษาอังกฤษนอก glossary
    for m in re.findall(r'[A-Za-z]{2,}', text):
        if m.lower() not in glossary:
            print(f"[ENGLISH] {name}: {m}")
            break

print(f"\nสแกนเสร็จ {len(files)} ตอน")
```
ขยายเพิ่มได้: นับความถี่คำข้ามตอน (MONOTONY), ตรวจความยาว `len(text.replace(' ','').replace('\n',''))`, ตรวจ adjectives ซ้อนเกิน 2 ชั้น

---

*สกิลนี้คือ "ผู้ตรวจระดับชุดตอน" — โครงจาก novel-structure การเขียนจาก novel-episode*
