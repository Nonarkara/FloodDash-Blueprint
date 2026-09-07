# 6. Build-your-own roadmap / แผนสร้างเวอร์ชันของคุณเอง

[← Design Language](05-design-language.md) · [Next: Honest Limitations →](07-honest-limitations.md)

---

**EN.** This roadmap is deliberately **stack-agnostic** — it describes what
to build in each phase, not which language or framework to build it in.
Pick tools your team already knows; the ideas in this blueprint transfer to
any of them.

**TH.** แผนนี้ตั้งใจให้ **ไม่ผูกกับสแตกใดสแตกหนึ่ง** — บอกว่าแต่ละเฟสต้อง
สร้างอะไร ไม่ใช่ต้องใช้ภาษาหรือเฟรมเวิร์กอะไร เลือกเครื่องมือที่ทีมคุณถนัด
อยู่แล้ว แนวคิดในพิมพ์เขียวนี้ใช้ได้กับทุกเครื่องมือ

## Phase 0 — One pipeline, proven end to end / เฟส 0 — ท่อข้อมูลเดียว พิสูจน์ครบวงจร

**EN.** Before writing anything else, prove the smallest possible slice:
fetch one public API (start with a national water-level feed — it's the
richest signal), store rows in a database with the dedup constraint from
§2.3, and query "how many rows do I have now vs. five minutes ago." If that
number grows correctly across two polling cycles with zero duplicates, your
foundation is sound. **Do not build a UI yet.** This phase is done when you
can run a single SQL query and see real timestamps from a real government
server.

*Effort: a few days for one developer, including reading the target API's
quirks.*

**TH.** ก่อนเขียนอะไรอื่น พิสูจน์ส่วนที่เล็กที่สุดก่อน: ดึง API สาธารณะหนึ่ง
ตัว (เริ่มจากฟีดระดับน้ำระดับประเทศ — สัญญาณที่อุดมที่สุด) เก็บแถวลงฐานข้อมูล
พร้อมข้อจำกัดไม่ซ้ำจากหัวข้อ 2.3 แล้ว query ว่า "ตอนนี้มีกี่แถวเทียบกับห้า
นาทีก่อน" ถ้าตัวเลขนั้นโตขึ้นถูกต้องข้ามสองรอบการดึงโดยไม่มีแถวซ้ำเลย
รากฐานของคุณมั่นคง **อย่าเพิ่งสร้าง UI** เฟสนี้เสร็จเมื่อคุณรัน SQL query
เดียวแล้วเห็น timestamp จริงจากเซิร์ฟเวอร์รัฐบาลจริง

*ใช้เวลา: ไม่กี่วันสำหรับนักพัฒนาหนึ่งคน รวมเวลาอ่านข้อควรระวังของ API เป้า
หมาย*

## Phase 1 — All your data sources, health-checked / เฟส 1 — ทุกแหล่งข้อมูล ตรวจสุขภาพได้

**EN.** Extend the pattern from Phase 0 to every source you plan to use
(§3). Each source is its own small module with its own polling cadence.
Add: per-source run history (did it succeed last time? when?), exponential
backoff on failure, and a simple health-check endpoint that reports every
source's status. This phase is done when you can leave the system running
unattended overnight and come back to a full night of real data with no
manual intervention.

*Effort: one to two weeks, depending on how many sources and how many of
their quirks you hit (budget extra time for at least one government API
that behaves unexpectedly — you will find one).*

**TH.** ขยายแพทเทิร์นจากเฟส 0 ไปทุกแหล่งข้อมูลที่วางแผนใช้ (หัวข้อ 3) แต่ละ
แหล่งเป็นโมดูลเล็กของตัวเองพร้อมความถี่การดึงของตัวเอง เพิ่ม: ประวัติการรัน
ต่อแหล่งข้อมูล (ครั้งล่าสุดสำเร็จไหม เมื่อไหร่) exponential backoff เมื่อ
ล้มเหลว และ endpoint ตรวจสุขภาพง่าย ๆ ที่รายงานสถานะทุกแหล่ง เฟสนี้เสร็จ
เมื่อคุณปล่อยระบบรันข้ามคืนโดยไม่มีคนดูแล แล้วกลับมาเจอข้อมูลจริงเต็มคืนโดย
ไม่ต้องแทรกแซงเอง

*ใช้เวลา: หนึ่งถึงสองสัปดาห์ ขึ้นกับจำนวนแหล่งข้อมูลและข้อควรระวังที่เจอ
(กันเวลาไว้เผื่อ API รัฐบาลอย่างน้อยหนึ่งตัวที่ทำงานผิดคาด — คุณจะเจอแน่นอน)*

## Phase 2 — The score, the map, the tap / เฟส 2 — คะแนน แผนที่ ท่อข้อมูลสด

**EN.** Now build the presentation layer: the watch-score formula (§4.1),
a map showing every station coloured by its current status, a ranked list
of the highest-scoring regions, and the live "tap" feed (§2.4) so users see
new data arrive in real time. This is the phase where the product becomes
demonstrably useful to a non-technical stakeholder — budget real design
time here, and test on an actual phone in actual sunlight, not just your
laptop screen indoors.

*Effort: two to four weeks, depending on map-library familiarity and how
much custom design polish you want.*

**TH.** ตอนนี้สร้างชั้นแสดงผล: สูตรคะแนนเฝ้าระวัง (หัวข้อ 4.1) แผนที่ที่
แสดงทุกสถานีตามสีสถานะปัจจุบัน รายการจัดอันดับพื้นที่ที่คะแนนสูงสุด และฟีด
"ท่อข้อมูลสด" (หัวข้อ 2.4) ให้ผู้ใช้เห็นข้อมูลใหม่มาถึงแบบเรียลไทม์ นี่คือ
เฟสที่ผลิตภัณฑ์มีประโยชน์ชัดเจนต่อผู้มีส่วนได้ส่วนเสียที่ไม่ใช่สายเทคนิค —
กันเวลาออกแบบจริงไว้ตรงนี้ และทดสอบบนมือถือจริงในแดดจริง ไม่ใช่แค่จอแล็ปท็อป
ในร่ม

*ใช้เวลา: สองถึงสี่สัปดาห์ ขึ้นกับความคุ้นเคยกับไลบรารีแผนที่และความพิถีพิถัน
ด้านดีไซน์ที่ต้องการ*

## Phase 3 — Connected waterways and soil memory / เฟส 3 — เส้นทางน้ำเชื่อมโยงและความจำของดิน

**EN.** Add the two refinements from §4.2–4.4: the upstream–downstream
cascade graph with GloFAS discharge, and the Antecedent Precipitation Index
computed from your own stored rain history. Both are pure additions on top
of Phase 2's data — no new data sources required beyond what GloFAS already
gives you. This is where the system stops being "a live map" and starts
being "a system that understands how flooding actually propagates."

*Effort: one to two weeks — mostly modeling work (defining your reaches
and their lag times) rather than plumbing, since the data pipeline pattern
is already proven.*

**TH.** เพิ่มส่วนขยายสองอย่างจากหัวข้อ 4.2–4.4: กราฟต้นน้ำ-ปลายน้ำพร้อม
อัตราการไหล GloFAS และดัชนีฝนสะสมถ่วงเวลาที่คำนวณจากประวัติฝนที่ระบบเก็บเอง
ทั้งสองเป็นส่วนเพิ่มล้วน ๆ บนข้อมูลจากเฟส 2 — ไม่ต้องมีแหล่งข้อมูลใหม่นอกจาก
ที่ GloFAS ให้อยู่แล้ว นี่คือจุดที่ระบบเลิกเป็น "แผนที่สด" แล้วกลายเป็น
"ระบบที่เข้าใจว่าน้ำท่วมแพร่กระจายอย่างไรจริง ๆ"

*ใช้เวลา: หนึ่งถึงสองสัปดาห์ — ส่วนใหญ่เป็นงานสร้างแบบจำลอง (กำหนดจุดในลำน้ำ
และเวลาเดินทาง) มากกว่างานท่อข้อมูล เพราะแพทเทิร์นท่อข้อมูลพิสูจน์แล้ว*

## Phase 4 — Trust and transparency features / เฟส 4 — ฟีเจอร์ความน่าเชื่อถือและความโปร่งใส

**EN.** Add threshold-crossing alerts with a cooldown (so a station
oscillating near a threshold doesn't spam), a searchable knowledge base
explaining your methodology in plain language (bilingual, if relevant),
and — only once everything above is solid — an optional local-LLM chat
layer that answers questions **grounded only in your database's real
numbers**, with a graceful, honest fallback when the model is unavailable.

*Effort: two to three weeks. The alerting and knowledge base are
straightforward; the chat layer is worth the extra care to get the
grounding right — a chatbot that hallucinates a water level is worse than
no chatbot.*

**TH.** เพิ่มการแจ้งเตือนเมื่อข้ามเกณฑ์พร้อม cooldown (เพื่อไม่ให้สถานีที่
แกว่งใกล้เกณฑ์สแปมการแจ้งเตือน) ฐานความรู้ที่ค้นหาได้ซึ่งอธิบายวิธีการของ
คุณด้วยภาษาธรรมดา (สองภาษาถ้าเกี่ยวข้อง) และ — เมื่อทุกอย่างข้างบนมั่นคง
แล้วเท่านั้น — ชั้นแชท LLM local ทางเลือกที่ตอบคำถาม **โดยอิงจากตัวเลขจริง
ในฐานข้อมูลเท่านั้น** พร้อม fallback ที่ซื่อสัตย์เมื่อโมเดลใช้ไม่ได้

*ใช้เวลา: สองถึงสามสัปดาห์ การแจ้งเตือนและฐานความรู้ตรงไปตรงมา ส่วนชั้นแชท
คุ้มค่าที่จะดูแลเป็นพิเศษให้การอ้างอิงถูกต้อง — แชทบอทที่มโนระดับน้ำแย่กว่า
ไม่มีแชทบอทเลย*

## Phase 5 — Operate it like it matters / เฟส 5 — ดูแลระบบเหมือนมันสำคัญจริง

**EN.** Install it as a real 24/7 service (not "leave a terminal window
open"), with automatic restart on crash, log rotation, and a data-retention
policy (raw readings for a bounded window, permanent hourly rollups after
that — §2.3). Do a real soak test: run it for at least a week unattended,
then check that memory use is stable, the database size matches your
estimate, and the live feed survived a machine sleep/wake cycle. **A flood
dashboard that goes down during a flood has negative value** — this phase
is not optional polish, it's the point.

*Effort: a few days, but budget a full week of unattended soak-testing
before you trust it in production.*

**TH.** ติดตั้งเป็นบริการ 24 ชั่วโมงจริง (ไม่ใช่ "เปิดหน้าต่างเทอร์มินัลทิ้ง
ไว้") พร้อมรีสตาร์ทอัตโนมัติเมื่อพัง หมุนเวียนล็อก และนโยบายเก็บข้อมูล
(ข้อมูลดิบในช่วงจำกัด สรุปรายชั่วโมงถาวรหลังจากนั้น — หัวข้อ 2.3) ทดสอบ
ทนทานจริง: รันอย่างน้อยหนึ่งสัปดาห์โดยไม่มีคนดูแล แล้วเช็คว่าการใช้หน่วยความ
จำนิ่ง ขนาดฐานข้อมูลตรงกับที่ประมาณไว้ และฟีดสดรอดจากรอบเครื่องหลับ/ตื่น
**แดชบอร์ดน้ำท่วมที่ล่มระหว่างน้ำท่วมมีค่าติดลบ** เฟสนี้ไม่ใช่การขัดเกลา
เสริม แต่คือประเด็นหลัก

*ใช้เวลา: ไม่กี่วัน แต่กันเวลาไว้หนึ่งสัปดาห์เต็มสำหรับทดสอบทนทานโดยไม่มีคน
ดูแลก่อนเชื่อใจให้ใช้งานจริง*

## Total rough estimate / ประมาณการรวมคร่าว ๆ

**EN.** Two developers, working through Phases 0–5 in order, should have a
genuinely useful, honest, 24/7-operable system in **roughly 8–12 weeks**,
faster if your team already knows the map library and database you choose.
That is a fraction of the procurement timeline for a commercial system —
and every number on screen will be one your own team can explain.

**TH.** นักพัฒนาสองคน ทำตามเฟส 0–5 ตามลำดับ ควรมีระบบที่มีประโยชน์จริง
ซื่อสัตย์ และรัน 24 ชั่วโมงได้ ใน **ประมาณ 8–12 สัปดาห์** เร็วกว่านั้นถ้า
ทีมคุณคุ้นเคยกับไลบรารีแผนที่และฐานข้อมูลที่เลือกอยู่แล้ว นั่นคือเสี้ยวหนึ่ง
ของระยะเวลาจัดซื้อระบบเชิงพาณิชย์ — และตัวเลขทุกตัวบนหน้าจอจะเป็นสิ่งที่ทีม
ของคุณเองอธิบายได้

When Phase 5 starts to hurt, read **[lessons from production](08-lessons-from-production.md)**
(tunnel hostnames, backups, irreplaceable SQLite, Pages proxy) and the
**[compute kit](09-compute-and-data.md)** (public URLs, schema, blank-Mac
checklist). Do not scrape [flood.nonarkara.org](https://flood.nonarkara.org)
to skip those files.

---

[← Design Language](05-design-language.md) · [Next: Honest Limitations →](07-honest-limitations.md)
