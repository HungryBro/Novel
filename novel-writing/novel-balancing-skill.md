---
name: novel-balancing
description: ทักษะการเกลี่ยและเฉลี่ยจำนวนอักขระของนิยายแต่ละตอนให้เท่ากันโดยอัตโนมัติ (Approach A) พร้อมการปรับเกลาจุดเชื่อมต่อของตอนและอัปเดตไฟล์บันทึกความต่อเนื่อง (Continuity Log)
---

# Skill: การเกลี่ยและเฉลี่ยจำนวนอักขระนิยาย (Novel Episode Balancing)

คู่มือและขั้นตอนการทำงานนี้ออกแบบมาสำหรับการจัดสรรเนื้อหาตอนนิยายในกรณีที่ความยาวแต่ละตอนไม่เท่ากัน (เช่น บางตอนยาว 7,000 บางตอนสั้น 2,000) โดยใช้วิธี **Paragraph Redistribution & Boundary Smoothing (การจัดสรรพารากราฟและขัดเกลารอยต่อ)** เพื่อให้ทุกตอนยาวเท่า ๆ กันและผ่านเกณฑ์มาตรฐานของโปรเจกต์ (3,000–4,000 อักขระแบบไม่รวมเว้นวรรค)

---

## 1. หลักการทำงานหลัก (Core Principles)
- **รักษาเนื้อหาดั้งเดิมครบถ้วน**: ไม่ใช้วิธีหั่นคำแบบสุ่ม แต่จะเกลี่ยเนื้อหาระดับ **ย่อหน้า (Paragraph)** เพื่อรักษารูปประโยคและความต่อเนื่อง
- **กำหนดเป้าหมายอักขระที่แม่นยำ**: คำนวณหาค่าเฉลี่ยตัวอักษรจริงของเรื่อง (`อักขระทั้งหมด / จำนวนตอน`)
- **ขัดเกลารอยต่อและโครงสร้างตอนใหม่**: เนื่องจากตอนถูกแบ่งใหม่ จุดหัว/ท้ายตอนจึงเปลี่ยนไป ต้องขัดเกลาบทบรรยาย/บทสนทนาใหม่เพื่อไม่ให้รอยต่ออ่านแล้วสะดุด
- **อัปเดตข้อมูลสรุปและบันทึกความต่อเนื่อง**: ปรับหัวข้อตอน, Editor Notes, Canon Facts และข้อมูลใน Continuity Log ให้ตรงกับเนื้อหาบทใหม่เสมอ

---

## 2. ขั้นตอนการดำเนินงาน (Workflow Step-by-Step)

### ขั้นตอนที่ 1: การประมวลผลและการรวมเนื้อหาดิบ (Extraction & Merging)
แยกเฉพาะส่วนของเนื้อเรื่องหลัก (Story Part) ออกจากข้อมูลเสริม (Editor Notes/Canon Facts ท้ายตอน) โดยตัดเนื้อหาที่อยู่หลังเครื่องหมายแถบแบ่ง `---` บรรทัดแรกออก จากนั้นนำพารากราฟทั้งหมดมารวมกันในอาเรย์เดียว

### ขั้นตอนที่ 2: แบ่งกลุ่มย่อหน้าตามเป้าหมายอักขระ (Greedy Redistribution)
ใช้โค้ดในการวนลูปนับจำนวนอักขระ (ไม่รวมเว้นวรรคและขึ้นบรรทัดใหม่) แล้วจับกลุ่มย่อหน้าเพื่อสร้างตอนใหม่ให้ใกล้เคียงกับความยาวเฉลี่ยที่เป็นเป้าหมายมากที่สุด

### ขั้นตอนที่ 3: ตรวจสอบและเกลาจุดรอยต่อ (Boundary Inspection & Smoothing)
หลังจากแบ่งกลุ่มดิบแล้ว ให้เข้าตรวจสอบจุดเริ่มต้นและจุดสิ้นสุดของแต่ละตอน โดยแก้ไขประเด็นที่มักจะเกิดความสะดุดในการอ่านดังนี้:
1. **การสะกดประโยคขาด**: หากตอนสิ้นสุดด้วยประโยคคำพูด หรือเครื่องหมายโคลอน `:` ให้แก้ไขให้เป็นประโยคที่สมบูรณ์ปิดท้ายตอน
2. **การแยกบทสนทนา**: หากมีบทคำถามและคำตอบคู่กันถูกจับแยกตอน ให้เกลี่ยประโยคให้อยู่ในตอนเดียวกันหรือย้ายไปเริ่มตอนถัดไป
3. **คำสรรพนามชี้เฉพาะ**: ตรวจสอบคำสรรพนามชี้เฉพาะที่ขึ้นต้นบท เช่น "พวกมัน" หรือ "เขา" ให้เปลี่ยนเป็นคำระบุชื่อตัวละครที่ชัดเจนเพื่อให้ผู้อ่านไม่สับสนเมื่อเริ่มบทใหม่
4. **ความน่าติดตาม (Hook & Cliffhanger)**: ตกแต่งคำลงท้ายให้น่าติดตามหรือจบตอนด้วยจังหวะที่ทิ้งปริศนา (Cliffhanger) หรือจุดพักอารมณ์ที่เหมาะสม

### ขั้นตอนที่ 4: สร้างข้อมูล Metadata และอัปเดตไฟล์ประกอบใหม่
1. **ตั้งชื่อตอนใหม่**: ตั้งชื่อตอนที่สื่อถึงจุดเด่นพล็อตเรื่องภายในตอนนั้น ๆ
2. **หมายเหตุบรรณาธิการ (Editor Notes)**: เขียนสรุปเจตนาการเล่าเรื่องของตอนและคำนวณจำนวนอักขระจริงใหม่
3. **สรุปข้อมูลที่เป็นทางการ (Canon Facts)**: เขียนตารางข้อมูลตัวละครที่ปรากฏ, สถานที่, และความก้าวหน้าพล็อตเรื่องเฉพาะภายในขอบเขตเนื้อหาใหม่
4. **Continuity Log**: เปิดไฟล์บันทึกความต่อเนื่องกลาง และเปลี่ยนตัวเลขอ้างอิงเลขตอน (เช่น ตอนที่ปรากฏตัวครั้งแรก, ลำดับเวลา Timeline) ให้สอดคล้องกับเนื้อหาที่ย้ายไป

### ขั้นตอนที่ 5: คอมไพล์และตรวจสอบความถูกต้อง (Compiling & Verification)
1. รันสคริปต์คอมไพล์นิยาย (เช่น `compile_arc1.py`) เพื่อประกอบเป็นไฟล์ `.docx` ปลายทาง
2. ตรวจสอบการตั้งค่าหน้ากระดาษ ขนาดฟอนต์ (TH Sarabun New) และโครงสร้างเลขตอนว่าสมบูรณ์ตามที่กำหนด

---

## 3. สคริปต์สนับสนุน (Helper Scripts)

คุณสามารถใช้สคริปต์ Python ด้านล่างนี้เพื่อช่วยในการดำเนินงาน

### 3.1 สคริปต์นับจำนวนอักขระจริง (Character Counter)
สคริปต์สำหรับนับจำนวนอักขระแยกตามตอนโดยหักเว้นวรรคและการขึ้นบรรทัดใหม่ออก เพื่อตรวจเช็คความสมดุลจริง
```python
import os

episodes_dir = '/Users/dolphin/Desktop/Novel/protocol-arceus/03-episodes'
for i in range(1, 21):
    filename = f"episode-{i:02d}.md"
    filepath = os.path.join(episodes_dir, filename)
    if not os.path.exists(filepath):
        print(f"Ep {i:02d}: Not found")
        continue
    with open(filepath, "r", encoding="utf-8") as f:
        content = f.read()
    
    # ดึงเฉพาะส่วนเนื้อเรื่องหลักก่อนเครื่องหมายแบ่งหน้า
    story_part = content.split("---")[0].strip()
    
    # นับจำนวนอักษรโดยตัดช่องว่างและอักษรควบคุม
    chars_no_space = len(story_part.replace(" ", "").replace("\n", "").replace("\r", "").replace("\t", ""))
    chars_with_space = len(story_part)
    
    print(f"Ep {i:02d}: {chars_no_space} chars (no space), {chars_with_space} chars (total)")
```

### 3.2 สคริปต์กระจายย่อหน้าดิบ (Greedy Redistributor)
สคริปต์สำหรับอ่านพารากราฟทั้งหมดแล้วรวมกลุ่มใหม่แบ่งออกเป็น 20 ตอนเท่า ๆ กันในไดเรกทอรีชั่วคราว
```python
import os

episodes_dir = '/Users/dolphin/Desktop/Novel/protocol-arceus/03-episodes'
out_dir = './scratch/redistributed'
os.makedirs(out_dir, exist_ok=True)

all_paragraphs = []
for i in range(1, 21):
    filename = f"episode-{i:02d}.md"
    filepath = os.path.join(episodes_dir, filename)
    if not os.path.exists(filepath):
        continue
    with open(filepath, "r", encoding="utf-8") as f:
        content = f.read()
    story_part = content.split("---")[0].strip()
    lines = story_part.split("\n")
    
    for line_idx, line in enumerate(lines[1:], start=2):
        if line.strip():
            all_paragraphs.append({
                "text": line.strip(),
                "src_ep": i,
                "src_line": line_idx
            })

def get_char_count(text):
    return len(text.replace(" ", "").replace("\n", "").replace("\r", "").replace("\t", ""))

total_chars = sum(get_char_count(p["text"]) for p in all_paragraphs)
target_chars = total_chars / 20

chapters = []
current_chapter = []
current_chars = 0

for p in all_paragraphs:
    p_chars = get_char_count(p["text"])
    if len(chapters) < 19:
        if current_chars > 0 and abs((current_chars + p_chars) - target_chars) > abs(current_chars - target_chars):
            chapters.append(current_chapter)
            current_chapter = [p]
            current_chars = p_chars
        else:
            current_chapter.append(p)
            current_chars += p_chars
    else:
        current_chapter.append(p)
        current_chars += p_chars

if current_chapter:
    chapters.append(current_chapter)

# เขียนไฟล์ผลลัพธ์ดิบ
for idx, ch in enumerate(chapters, 1):
    filename = f"episode-{idx:02d}_raw.md"
    filepath = os.path.join(out_dir, filename)
    with open(filepath, "w", encoding="utf-8") as out_f:
        out_f.write(f"# RAW EPISODE {idx:02d}\n\n")
        for p in ch:
            out_f.write(p["text"] + "\n\n")
```
