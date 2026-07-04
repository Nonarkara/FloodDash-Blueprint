# 5. Design language: two languages, one screen, zero decoration
## ปรัชญาการออกแบบ: สองภาษา หนึ่งหน้าจอ ไม่มีการตกแต่งเกินจำเป็น

[← The Science](04-the-science.md) · [Next: Build-Your-Own Roadmap →](06-build-your-own-roadmap.md)

---

## 5.1 The governing idea / แนวคิดหลัก

**EN.** Borrow from two design traditions that already solved "how do you
show a lot of live, safety-critical information to a stranger under time
pressure": **Dieter Rams' functionalist industrial design** ("as little
design as possible") and **the New York City subway signage system**
(Vignelli/Noorda-era) — a system built entirely around the idea that a
lost, stressed person in an unfamiliar environment must be able to parse
status at a glance, in more than one language, without decoration
competing with information.

Concretely, this means: **no rounded corners, no drop shadows, no gradients
as decoration.** Depth and hierarchy come from colour and contrast alone.
Every changing number is set in a monospaced font so digits don't jitter
the layout as they update. Status is communicated with small, consistent,
colour-coded badges — the same visual grammar a subway map uses for its
route bullets — never colour alone (colour-blind users must be able to
read a badge's *shape* and *label*, not just its hue).

**TH.** ยืมจากสองประเพณีการออกแบบที่แก้โจทย์ "จะแสดงข้อมูลสดจำนวนมากที่
เกี่ยวกับความปลอดภัยให้คนแปลกหน้าดูภายใต้แรงกดดันด้านเวลาได้อย่างไร" ไว้แล้ว:
**การออกแบบอุตสาหกรรมแบบฟังก์ชันนัลลิสต์ของ Dieter Rams** ("ออกแบบให้น้อย
ที่สุดเท่าที่จะทำได้") และ **ระบบป้ายรถไฟใต้ดินนิวยอร์ก** (ยุค Vignelli/
Noorda) — ระบบที่สร้างขึ้นทั้งหมดบนแนวคิดที่ว่าคนที่หลงทางและเครียดในสภาพ
แวดล้อมที่ไม่คุ้นเคยต้องอ่านสถานะได้ในพริบตา มากกว่าหนึ่งภาษา โดยไม่มีการ
ตกแต่งมาแข่งกับข้อมูล

พูดให้เป็นรูปธรรม: **ไม่มีมุมโค้ง ไม่มีเงาตก ไม่มีเกรเดียนต์เพื่อการตกแต่ง**
ความลึกและลำดับชั้นมาจากสีและคอนทราสต์อย่างเดียว ตัวเลขที่เปลี่ยนแปลงทุกตัว
ใช้ฟอนต์ monospace เพื่อไม่ให้ตัวเลขกระตุกเลย์เอาต์เมื่ออัปเดต สถานะสื่อสาร
ด้วยแบดจ์สีเล็ก ๆ ที่สม่ำเสมอ — ไวยากรณ์ภาพเดียวกับที่แผนที่รถไฟใต้ดินใช้กับ
สัญลักษณ์สายรถ — ไม่ใช้สีอย่างเดียวเด็ดขาด (ผู้ใช้ตาบอดสีต้องอ่าน *รูปทรง*
และ *ป้ายกำกับ* ของแบดจ์ได้ ไม่ใช่แค่สี)

## 5.2 Bilingual signage, not a language toggle for everything / ป้ายสองภาษา ไม่ใช่ปุ่มสลับภาษาทุกที่

**EN.** This is the single most important, most-often-gotten-wrong idea in
this whole document, so read it twice. There are **two different kinds of
text** in a bilingual interface, and they should be treated completely
differently:

1. **Permanent signage** — section labels, navigation, chrome. Show
   **both languages at once, always**, the way a real subway sign shows
   both the local script and English underneath, all the time, for every
   rider. This text should **never change** when a user flips a language
   toggle, because it was never in one language to begin with.
2. **Dynamic content** — data values, generated prose, chat responses.
   This is what a language toggle actually controls: which language the
   *content* renders in.

The failure mode to avoid, because a real implementation shipped it and had
to fix it: if your "toggle chrome labels" code path rewrites the *signage*
elements too, you get one of two visible bugs. Either the small permanent
caption duplicates the main label (`"ALERTS ALERTS"`) once the toggle
matches the caption's language, or — worse — if that signage element also
happens to contain a live, JS-managed child (a counter badge, for
instance), rewriting its markup on every toggle **silently destroys that
child element**, and the counter breaks permanently after the very first
language switch. The fix is architectural, not a patch: **decide up front
which elements are signage (static, bilingual, untouched by the toggle) and
which are content (dynamic, single-language, toggle-driven), and never let
one code path touch both.**

**TH.** นี่คือแนวคิดที่สำคัญที่สุดและถูกทำผิดบ่อยที่สุดในเอกสารทั้งหมดนี้
อ่านสองรอบ มี **ข้อความสองแบบ** ในอินเทอร์เฟซสองภาษา และควรจัดการต่างกัน
โดยสิ้นเชิง:

1. **ป้ายถาวร** — ป้ายกำกับหมวด การนำทาง โครงหน้าจอ แสดง **ทั้งสองภาษา
   พร้อมกันเสมอ** แบบเดียวกับป้ายรถไฟใต้ดินจริงที่แสดงทั้งภาษาท้องถิ่นและ
   ภาษาอังกฤษข้างล่างตลอดเวลา สำหรับผู้โดยสารทุกคน ข้อความนี้ **ไม่ควรเปลี่ยน
   เลย** เมื่อผู้ใช้กดปุ่มสลับภาษา เพราะมันไม่เคยเป็นภาษาเดียวตั้งแต่แรก
2. **เนื้อหาที่เปลี่ยนแปลง** — ค่าข้อมูล ข้อความที่สร้างขึ้น คำตอบแชท นี่คือ
   สิ่งที่ปุ่มสลับภาษาควบคุมจริง ๆ: *เนื้อหา* จะแสดงเป็นภาษาไหน

รูปแบบความล้มเหลวที่ควรหลีกเลี่ยง เพราะระบบต้นแบบจริงเคยพลาดแล้วต้องแก้: ถ้า
โค้ด "สลับป้ายโครงหน้าจอ" ไปเขียนทับ *องค์ประกอบป้าย* ด้วย จะได้บั๊กที่เห็น
ได้สองแบบ อย่างแรกคือคำบรรยายเล็กถาวรซ้ำกับป้ายหลัก (`"ALERTS ALERTS"`)
เมื่อปุ่มสลับตรงกับภาษาของคำบรรยาย หรือแย่กว่านั้น — ถ้าองค์ประกอบป้ายนั้น
บังเอิญมีลูกที่ JS จัดการอยู่ (เช่น แบดจ์ตัวนับ) การเขียนทับ markup ทุกครั้ง
ที่สลับภาษาจะ **ทำลายองค์ประกอบลูกนั้นแบบเงียบ ๆ** และตัวนับพังถาวรหลังสลับ
ภาษาครั้งแรก ทางแก้คือเชิงสถาปัตยกรรม ไม่ใช่แค่ปะ: **ตัดสินใจล่วงหน้าว่า
องค์ประกอบไหนเป็นป้าย (ถาวร สองภาษา ปุ่มสลับแตะต้องไม่ได้) และองค์ประกอบไหน
เป็นเนื้อหา (เปลี่ยนแปลง ภาษาเดียว ปุ่มสลับควบคุม) แล้วอย่าให้โค้ดพาธเดียว
แตะทั้งสองอย่าง**

## 5.3 A starting palette / จานสีเริ่มต้น

**EN.** A reference palette that reads as "official, calm, in-control" for
a Thai government context, built around the national flag's own colours so
red carries genuine cultural weight as an emergency signal (never spend it
on decoration):

| Role | Example value | Why |
|---|---|---|
| Ground / background | warm off-white (not pure white) | softer on eyes for a screen meant to run 24/7 |
| Ink / primary text | a deep navy (not pure black) | matches the flag's blue, reads as "official" without being cold |
| Normal / low severity | green | universal "OK" |
| Watch | amber/yellow | universal "attention" |
| Elevated | orange | universal "escalating" |
| Critical / overflow | the flag's red, and **only** this colour, reserved exclusively for genuine emergency states | when red always means real danger, users learn to trust it instantly |

Typography: one humanist sans that has excellent Thai-script support for
all running text and UI labels, paired with one monospaced face reserved
**exclusively** for numbers that change — this single pairing decision does
more for perceived quality than any other typographic choice.

**TH.** จานสีอ้างอิงที่อ่านได้ว่า "เป็นทางการ สงบ ควบคุมได้" สำหรับบริบท
ราชการไทย สร้างจากสีธงชาติเอง เพื่อให้สีแดงมีน้ำหนักทางวัฒนธรรมจริงในฐานะ
สัญญาณฉุกเฉิน (อย่าใช้กับการตกแต่งเด็ดขาด):

| บทบาท | ตัวอย่างค่า | เหตุผล |
|---|---|---|
| พื้น/พื้นหลัง | ขาวอมครีมอุ่น (ไม่ใช่ขาวบริสุทธิ์) | สบายตากว่าสำหรับจอที่รันตลอด 24 ชม. |
| หมึก/ข้อความหลัก | น้ำเงินเข้ม (ไม่ใช่ดำบริสุทธิ์) | ตรงกับสีน้ำเงินธงชาติ อ่านว่า "เป็นทางการ" โดยไม่เย็นชา |
| ปกติ/ความรุนแรงต่ำ | เขียว | "โอเค" แบบสากล |
| เฝ้าระวัง | เหลืองอำพัน | "ต้องสนใจ" แบบสากล |
| เสี่ยงสูง | ส้ม | "กำลังทวีความรุนแรง" |
| วิกฤต/ล้นตลิ่ง | สีแดงของธงชาติ และสีนี้ **เท่านั้น** สงวนไว้เฉพาะภาวะฉุกเฉินจริง | เมื่อสีแดงหมายถึงอันตรายจริงเสมอ ผู้ใช้เรียนรู้ที่จะเชื่อทันที |

ตัวอักษร: ฟอนต์ humanist sans หนึ่งตัวที่รองรับภาษาไทยดีเยี่ยมสำหรับข้อความ
วิ่งและป้ายกำกับ UI ทั้งหมด คู่กับฟอนต์ monospace หนึ่งตัวที่สงวนไว้
**เฉพาะ** ตัวเลขที่เปลี่ยนแปลง — การตัดสินใจคู่ฟอนต์นี้อย่างเดียวช่วยให้รู้สึก
ถึงคุณภาพมากกว่าการตัดสินใจเรื่องตัวอักษรอื่นใด

## 5.4 Fixed viewport, internal scroll / วิวพอร์ตคงที่ เลื่อนภายใน

**EN.** On a control-room display or a phone held by someone standing in
rain, the map must never scroll off screen. Build the layout as a fixed
grid (header, map, side panels, ticker) sized to exactly the viewport
height, and make **only the panels' interior content scrollable**. On
narrow screens, collapse side panels into a bottom sheet with tabs, but
keep the same rule: the map stays, everything else scrolls inside itself.

**TH.** บนจอห้องควบคุมหรือมือถือของคนที่ยืนอยู่กลางฝน แผนที่ต้องไม่เลื่อน
หายไปจากหน้าจอเด็ดขาด สร้างเลย์เอาต์เป็นกริดคงที่ (หัวกระดาษ แผนที่ แผงข้าง
แถบข่าววิ่ง) ขนาดพอดีความสูงวิวพอร์ต และให้ **เฉพาะเนื้อหาภายในแผงเท่านั้น**
ที่เลื่อนได้ บนจอแคบ ยุบแผงข้างเป็นแผ่นล่างพร้อมแท็บ แต่รักษากติกาเดียวกัน:
แผนที่อยู่ที่เดิม ทุกอย่างที่เหลือเลื่อนภายในตัวเอง

---

[← The Science](04-the-science.md) · [Next: Build-Your-Own Roadmap →](06-build-your-own-roadmap.md)
