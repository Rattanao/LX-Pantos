# Check EDI LX Pantos

เครื่องมือตรวจสอบเอกสารขาเข้าเรือก่อนจัดทำใบขนสินค้าขาเข้า (Thailand ocean import) —
เทียบ **MANIFEST** (รายงานจากสายเรือ, .xls/.xlsx หรือ PDF "CARGO MANIFEST") กับ **ENTER**
(ฟอร์ม AMENDMENT OF MANIFEST ของ LX PANTOS, .pdf) ทีละ B/L ย่อย แล้วสร้างรายงาน `EDI.xlsx` แบบไฮไลต์สี
บอกจุดที่ต้องแก้ก่อนยื่นใบขน

> **ข้อจำกัด:** หน้าเว็บรองรับเฉพาะ **ENTER ฟอร์มของ LX Pantos**
> ถ้าเจอฟอร์มของเจ้าอื่นหน้าเว็บจะแจ้งว่าไม่ใช่ฟอร์มของ LX Pantos · ไฟล์ CNTRS ไม่นำมาเทียบ

## Check EDI LX Pantos บนเว็บ (ให้เพื่อนใช้ได้ ไม่ต้องติดตั้ง)

https://rattanao.github.io/LX-Pantos/

1. ลากไฟล์ **MANIFEST** (.xls/.xlsx หรือ PDF) และ **ENTER ของ LX Pantos ทุกไฟล์** (ไฟล์ละ 1 B/L) มาวางพร้อมกัน
   (ตั้งชื่ออะไรก็ได้ — PDF ที่เป็น CARGO MANIFEST จะถูกจัดเป็น MANIFEST ให้เอง, PDF อื่นเป็น ENTER;
   ไฟล์ที่ชื่อมี `CNTRS` จะถูกข้าม)
2. ใส่ SHED NO. ที่แจ้ง (ถ้ามี) แล้วกด **ตรวจสอบ**
3. ดูผลบนหน้าเว็บ (ตารางสีเหมือน EDI.xlsx, ค้นหา/กรอง "แสดงเฉพาะที่ไม่ตรง") หรือกด **ดาวน์โหลด EDI.xlsx**

ไฟล์ถูกประมวลผลในเบราว์เซอร์ของผู้ใช้เท่านั้น ไม่มีการอัปโหลดไปที่ใด

โค้ดหน้าเว็บอยู่ที่ `docs/` (`index.html` + `core.js` — พอร์ตจาก `build_edi.py` layout J,
ฐานเดียวกับ [Check EDI Union World](https://rattanao.github.io/Union-world/) และ
[Check EDI Able](https://rattanao.github.io/check-edi-Able/))

## ฟอร์ม ENTER ที่รองรับ

หน้าละ 1 B/L (ลูกค้ามักส่งมาไฟล์ละ B/L เช่น `ENTER HASLK01260000521.pdf`, `ENTER HASLK01260000521A.pdf` …):

```
B/L NO.       : HASLK01260000521
MARK                   NO PACKAGES      DESCRIPTION OF GOODS
D.S.                   10 PACKAGES      PRE-SHAFT GEAR(HEDD)
(IN DIA)                                PRE-SHAFT SPIN(HEDD)
PKG :10(GT)
              (W:   8,086.000   KGS./M :   8.000   CBM.)
TOTAL         : 10 PACKAGES
CONSIGNEE     : ABC TRADING CO., LTD
NOTIFY PARTY  : SAME AS ABOVE
CONTAINER NO  : HALU1234567                          1*20DR
STATUS        : ***CY ลากตู้*** D/O รับแหลมฉบัง
```

- MARKS / NO PACKAGES / DESCRIPTION แยกตามตำแหน่งคอลัมน์ (x < 195 / 195–335 / ≥ 335)
- G.W. อยู่ระหว่าง `(W:` กับ `KGS./M :` และ MEAS อยู่ก่อน `CBM.)`
- NOTIFY `SAME AS ABOVE` = ผู้รับรายเดียวกับ CONSIGNEE
- STATUS: `CY`/`ลากตู้` = CY, `LCL`/`ขน` = LCL, `CFS`/`เปิดตู้` = LCL/CFS
- ฟอร์มนี้ไม่มียอดรวมทั้งเอกสาร — แถว TOTAL เทียบผลรวม ENTER กับ MANIFEST (และ GRAND TOTAL ของ MANIFEST)

## สิ่งที่ตรวจ

CONSIGNEE · TAX ID vs NOTIFY · CONTAINER · STATUS · จำนวน/ชนิดหีบห่อ · G.W. · MEAS. · MARKS · DESCRIPTION ·
REEFER · DG · TRANSIT · SHED NO. · ประเทศปลายทาง · CARGO MOVEMENT — สีแดง ⚠ = ไม่ตรง, ส้ม = ต้องยืนยัน,
เหลือง = ไม่มี ENTER เทียบ, เขียว = ผ่าน · B/L ที่มีใน ENTER แต่ไม่มีใน MANIFEST = วิกฤต

## ใช้แบบ Python (ในเครื่อง)

```
pip install -r requirements.txt
python build_edi.py --manifest input/MANIFEST.xls --enter ENTER_ALL.pdf --outdir output [--shed 0141]
```

`--enter` รับไฟล์เดียว — ถ้า ENTER มาเป็นไฟล์ละ B/L ให้รวมเป็น PDF เดียวก่อน (หน้าเว็บรับหลายไฟล์ได้เลย)

## ตรวจความตรงกันของหน้าเว็บกับ Python

2026-10-19: งาน SAWASDEE ALTAIR 2609S (7 B/L) ตรงกัน 224/224 ช่อง (ค่าและสี) ทั้งแบบไม่ใส่ SHED,
`--shed 2813` และ `--shed 0141`

ไม่มีข้อมูลลูกค้าจริงใน repo นี้ (`.gitignore` กัน `input/`, `output/`, `*.xls*`, `*.pdf`)

Project by Ms. Rattana Mudlae — Position Import DOC.
