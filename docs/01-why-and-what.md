# 1. Why this exists, and what it is / ที่มาและคืออะไร

[← README](../README.md) · [Next: Architecture →](02-architecture.md)

---

## The gap / ช่องว่าง

**EN.** In late 2025, Hat Yai flooded badly. It wasn't the first time — 2000,
2010, and 2017 all did the same thing to the same small, steep catchment
(Khlong U-Taphao) during the same season (the northeast monsoon, November–
January, when the rest of Thailand is dry but the Gulf coast gets its
heaviest rain of the year). Each time, the public conversation afterward
asked some version of: *"didn't we have the data to see this coming?"*

The honest answer is: **yes, almost all of it.** Thailand runs one of the
most comprehensive free hydrological data infrastructures in Southeast
Asia — HII's ThaiWater network alone has hundreds of telemetry stations
updating every 10–15 minutes, with no authentication required. The Royal
Irrigation Department, the Pollution Control Department, the Thai
Meteorological Department, and international sources (Copernicus GloFAS,
Open-Meteo, NOAA, NASA) all publish real, current, free data. **The
information exists. It is fragmented across a dozen agency portals, each
with its own update cadence, its own units, and — critically — no single
place where a human can see the whole system at once.**

That fragmentation is the actual problem this blueprint solves. Not a
sensor gap. An integration gap.

**TH.** ปลายปี 2568 หาดใหญ่ท่วมหนัก — ไม่ใช่ครั้งแรก ปี 2543, 2553, 2560
ก็ท่วมแบบเดียวกันในลุ่มน้ำเดียวกัน (คลองอู่ตะเภา) ในฤดูเดียวกัน (มรสุมตะวันออก
เฉียงเหนือ พ.ย.–ม.ค. ซึ่งภาคอื่นแล้งแต่ฝั่งอ่าวไทยฝนหนักที่สุดของปี) ทุกครั้ง
คำถามหลังเหตุการณ์คือ *"เรามีข้อมูลพอจะเห็นล่วงหน้าไหม"*

คำตอบตรง ๆ คือ **มี เกือบทั้งหมด** ไทยมีโครงสร้างข้อมูลอุทกวิทยาเปิดที่ครบ
ที่สุดแห่งหนึ่งในเอเชียตะวันออกเฉียงใต้ — เครือข่าย ThaiWater ของ สสน.
มีสถานีโทรมาตรหลายร้อยแห่ง อัปเดตทุก 10–15 นาที ไม่ต้องยืนยันตัวตน กรม
ชลประทาน กรมควบคุมมลพิษ กรมอุตุนิยมวิทยา และแหล่งข้อมูลนานาชาติ (Copernicus
GloFAS, Open-Meteo, NOAA, NASA) ต่างเผยแพร่ข้อมูลจริง ปัจจุบัน และฟรีเช่นกัน
**ข้อมูลมีอยู่ แต่กระจัดกระจายในพอร์ทัลของแต่ละหน่วยงาน ต่างความถี่ ต่างหน่วย
และที่สำคัญที่สุดคือไม่มีที่เดียวที่คนคนหนึ่งจะเห็นภาพรวมทั้งระบบได้พร้อมกัน**

นั่นคือปัญหาจริงที่พิมพ์เขียวนี้แก้ ไม่ใช่ช่องว่างของเซนเซอร์ แต่คือช่องว่าง
ของการบูรณาการ

## The founding premise / สมมติฐานตั้งต้น

**EN.** If the data is free, public, and machine-readable, then the entire
value of a flood dashboard is in **the integration layer** — the part that
fetches, validates, normalizes, stores, cross-references, and finally
explains nine different agencies' worth of numbers in one bilingual view
where every figure can be traced back to the exact reading that produced it.
Everything downstream of that (the map, the risk score, the chat assistant)
is presentation. Get the integration layer right — honest about gaps,
transparent about formulas, generous with provenance — and the rest follows.

This has three concrete implications for anyone building their own version:

1. **Single machine, not a cluster.** If the data volume is genuinely modest
   (a few hundred thousand rows a day across all sources), there is no
   reason to reach for Kubernetes, managed databases, or a message queue.
   A laptop that never sleeps and a single-file database can serve a whole
   province.
2. **Real data or nothing.** No synthetic numbers, no interpolated gaps, no
   "estimated" fallback values presented as if they were readings. If a
   station hasn't reported, the dashboard should say so, not paper over it.
3. **Every number must be explainable.** A provincial emergency operations
   center will ask "why does this say high risk" in the middle of a crisis.
   The answer must be a formula you can write on a whiteboard, not a black
   box.

**TH.** ถ้าข้อมูลฟรี เปิด และเครื่องอ่านได้อยู่แล้ว คุณค่าทั้งหมดของแดชบอร์ด
น้ำท่วมจึงอยู่ที่ **ชั้นบูรณาการ** — ส่วนที่ดึงข้อมูล ตรวจสอบ แปลงรูปแบบ
เก็บ เชื่อมโยง และสุดท้ายอธิบายตัวเลขจาก 9 หน่วยงานให้อยู่ในมุมมองสองภาษา
เดียวที่ตัวเลขทุกตัวย้อนกลับไปหาค่าที่วัดได้จริงได้ ทุกอย่างที่อยู่ปลายน้ำจากจุด
นั้น (แผนที่ คะแนนความเสี่ยง แชทบอท) เป็นแค่การนำเสนอ ทำชั้นบูรณาการให้ถูก —
ซื่อสัตย์กับช่องว่างข้อมูล โปร่งใสกับสูตร ใจกว้างกับการอ้างอิงที่มา — ที่เหลือ
จะตามมาเอง

มีนัยที่จับต้องได้ 3 ข้อสำหรับใครก็ตามที่จะสร้างเวอร์ชันของตัวเอง:

1. **เครื่องเดียว ไม่ใช่คลัสเตอร์** ถ้าปริมาณข้อมูลไม่ได้มหาศาลจริง (หลักแสน
   แถวต่อวันรวมทุกแหล่ง) ไม่มีเหตุผลต้องใช้ Kubernetes ฐานข้อมูลจัดการเอง หรือ
   message queue แล็ปท็อปที่ไม่หลับและฐานข้อมูลไฟล์เดียวรองรับทั้งจังหวัดได้
2. **ข้อมูลจริงหรือไม่มีเลย** ห้ามมีตัวเลขสังเคราะห์ ห้าม interpolate ช่องว่าง
   ห้ามมีค่า "ประมาณการ" ที่แสดงราวกับเป็นค่าที่วัดได้จริง ถ้าสถานีไม่รายงาน
   แดชบอร์ดต้องบอกตรง ๆ ไม่ใช่กลบเกลื่อน
3. **ทุกตัวเลขต้องอธิบายได้** ศูนย์ปฏิบัติการฉุกเฉินจังหวัดจะถามว่า "ทำไมขึ้น
   ความเสี่ยงสูง" กลางวิกฤต คำตอบต้องเป็นสูตรที่เขียนบนกระดานได้ ไม่ใช่กล่องดำ

## Who this is for / เหมาะกับใคร

**EN.** Provincial and municipal emergency operations centers, universities
with a GIS or civil-engineering programme, civic-tech volunteers, and any
government agency that wants a bilingual public-facing watch tool without
procuring a multi-million-baht system. If your team can write a scheduled
HTTP fetch and store rows in a database, you have the core skill needed.
Everything past that is choices, not obstacles.

**TH.** เหมาะกับศูนย์ปฏิบัติการฉุกเฉินระดับจังหวัดและเทศบาล มหาวิทยาลัยที่มี
หลักสูตร GIS หรือวิศวกรรมโยธา อาสาสมัคร civic-tech และหน่วยงานรัฐใด ๆ ที่
อยากมีเครื่องมือเฝ้าระวังสองภาษาแบบสาธารณะโดยไม่ต้องจัดซื้อระบบราคาหลักล้าน
บาท ถ้าทีมของคุณเขียนโปรแกรมดึงข้อมูลตามตารางเวลาและเก็บลงฐานข้อมูลได้ คุณมี
ทักษะหลักที่จำเป็นแล้ว ที่เหลือคือทางเลือก ไม่ใช่อุปสรรค

---

[← README](../README.md) · [Next: Architecture →](02-architecture.md)
