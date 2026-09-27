<!--
SPDX-FileCopyrightText: 2026 Dr Non Arkaraprasertkul
SPDX-License-Identifier: CC-BY-4.0
-->

# 10. Civic jobs and trust / งานที่พลเมืองต้องการ และความไว้ใจ

[← Compute kit](09-compute-and-data.md) · [Next: Citizen signals →](11-citizen-signals.md)

---

**EN.** A flood watch earns its keep in the hour someone forwards it.
The jobs below are generalised from civic use during flood season: the
kinds of questions a soi, a canal, and a municipality actually bring.
They are **jobs**, not a transcript. This page invents no quotation, no
chat log, and no water depth.

Fork the method. The private twin's conversations are not in this
repository, and they are not yours to reconstruct.

**TH.** ระบบเฝ้าระวังน้ำท่วมพิสูจน์ตัวเองในชั่วโมงที่มีคนส่งต่อ
งานด้านล่างสรุปจากการใช้จริงในฤดูน้ำท่วม: คำถามแบบที่ซอย คลอง และเทศบาล
นำมาจริง ๆ นี่คือ **งาน** ไม่ใช่บทถอดความ หน้านี้ไม่มีคำพูดที่แต่งขึ้น
ไม่มีบันทึกแชท และไม่มีระดับน้ำที่สมมติ

สืบทอดวิธี บทสนทนาของคู่แฝดส่วนตัวไม่อยู่ใน repository นี้ และไม่ใช่ของคุณที่จะรื้อมาสร้างใหม่

**EN.** Read this next to [honest limitations](07-honest-limitations.md), the
[watch-score sentence](04-the-science.md), and [citizen signals](11-citizen-signals.md).
If a shared link must survive a spike, the ops side of the same promise
is [§8.9](08-lessons-from-production.md).

**TH.** อ่านคู่กับ[ข้อจำกัดอย่างซื่อสัตย์](07-honest-limitations.md)
[ประโยคของคะแนนเฝ้าระวัง](04-the-science.md) และ[สัญญาณจากประชาชน](11-citizen-signals.md)
ถ้าลิงก์ที่แชร์ต้องรอดคลื่นการส่งต่อ ด้านปฏิบัติการของคำสัญญาเดียวกันอยู่ที่[หัวข้อ 8.9](08-lessons-from-production.md)

---

## 10.1 Answer the place first / ตอบที่ก่อน

**The job.** “ซอย/คลองฉันยังท่วมไหม” — “Is my soi, or my canal, still flooded?”

**EN.** The first sentence names *their* place and the honest state you
actually have: water reported, gauges quiet, or no reading since a time
you can show. Where the number came from, and how old it is, belongs in
the next sentence.

A system pitch can wait. People did not open the message to learn your
architecture, your agency count, or your roadmap. They asked about a
street. Lead with the street.

If you do not know, say you do not know, and say what you checked. A
plain gap is a usable answer. A paragraph about the platform is not.

**TH.** ประโยคแรกต้องระบุ *ที่ของเขา* และสถานะที่คุณมีจริง: มีรายงานว่ามีน้ำ
มาตรวัดเงียบ หรือไม่มีค่าตั้งแต่เวลาที่แสดงได้ ที่มาของตัวเลข และค่าอายุเท่าไร
อยู่ประโยคถัดไป

คำอธิบายระบบรอได้ คนไม่ได้เปิดข้อความมาเพื่อเรียนรู้สถาปัตยกรรม จำนวนหน่วยงาน
หรือแผนงาน เขาถามถนนสายหนึ่ง จงเริ่มที่ถนนสายนั้น

ถ้าไม่รู้ ก็บอกว่าไม่รู้ และบอกว่าตรวจอะไรไปแล้ว ช่องว่างที่พูดตรง ๆ เป็นคำตอบที่ใช้ได้
ย่อหน้าเกี่ยวกับแพลตฟอร์มไม่ใช่คำตอบ

---

## 10.2 Rain stopping is not a canal emptying / ฝนหยุด ไม่เท่ากับคลองว่าง

**The job.** “ฝนหยุดแล้วทำไมยังท่วม” — “The rain stopped. Why is it still flooded?”

**EN.** Both can be true at once. A gauge can show the rain has eased
while the soi, the drain, and the canal are still holding water. Soil
keeps a memory of earlier rain. A downstream level can block an outlet.
A gate, a pump, or a tide can matter more than the cloud that just left.

Say that in ordinary words. Point at the layer you actually have — a
rain gauge, a canal station, a published dam release — and label it
[measured or modelled](04-the-science.md). Teach the lag. Skip the
lecture about your stack.

Comfort that skips the lag is how a quiet radar gets mistaken for a dry
door. A quiet radar is a rain fact. A wet door is a water fact. Keep
both.

**TH.** ทั้งสองอย่างเป็นจริงพร้อมกันได้ มาตรวัดฝนอาจบอกว่าฝนเบาลง ขณะที่ซอย
ท่อ และคลองยังอุ้มน้ำอยู่ ดินจำฝนที่ตกไปแล้ว ระดับปลายน้ำอาจดันไม่ให้น้ำออก
ประตูระบาย ปั๊ม หรือน้ำขึ้นน้ำลง อาจสำคัญกว่าเมฆที่เพิ่งผ่านไป

พูดแบบภาษาคน ชี้ชั้นข้อมูลที่คุณมีจริง — มาตรวัดฝน สถานีคลอง การระบายเขื่อนที่หน่วยงานเผยแพร่ —
แล้วติดป้ายว่าเป็น[ค่าที่วัดได้หรือค่าที่จำลอง](04-the-science.md) สอนเรื่องเวลาที่น้ำยังค้าง
ข้ามการบรรยายเรื่องสแตก

การปลอบที่ข้ามเวลาหน่วง ทำให้เรดาร์ที่เงียบถูกเข้าใจผิดว่าประตูบ้านแห้ง
เรดาร์ที่เงียบคือข้อเท็จจริงของฝน ประตูที่ยังเปียกคือข้อเท็จจริงของน้ำ เก็บทั้งคู่ไว้

---

## 10.3 A high score is attention, not an evacuation / คะแนนสูงคือความสนใจ ไม่ใช่คำสั่งอพยพ

**The job.** “คะแนนสูง = อพยพ?” — “Does a high score mean evacuate?”

**EN.** A watch score is a heuristic for where to look. It is built
from live observations and labelled layers. Evacuation is a separate
decision, made by the agencies below. The sentence you owe every screen
is the one in [§4.1](04-the-science.md): a watch score is an indicator,
not an official warning.

Evacuation and safety calls belong to the agencies that hold that
mandate: **DDPM** (ปภ.), **TMD** (กรมอุตุนิยมวิทยา), and **ONWR** (สทนช.).
Thailand’s public disaster hotline is **1784**. Print it beside the
score, as a pointer to those agencies. Confirm the number on DDPM’s own
page when you publish. Do not invent a second hotline, and do not let
your municipality’s chat account sound like it has issued an order.

When someone is in immediate danger, the useful sentence is the official
channel. Your score can sit next to that sentence. It does not replace it.

**TH.** คะแนนเฝ้าระวังเป็นตัวชี้วัดเชิงประเมินว่าควรหันไปมองที่ไหน
มันสร้างจากการสังเกตสดและชั้นข้อมูลที่มีป้ายกำกับ การอพยพเป็นการตัดสินใจคนละเรื่อง
ทำโดยหน่วยงานด้านล่าง ประโยคที่คุณติดหนี้ทุกหน้าจอคือประโยคใน[หัวข้อ 4.1](04-the-science.md):
คะแนนเฝ้าระวังเป็นตัวชี้วัด ไม่ใช่ประกาศเตือนภัยทางการ

การอพยพและการเรียกด้านความปลอดภัยเป็นของหน่วยงานที่มีอำนาจนั้น: **ปภ.** (DDPM),
**กรมอุตุนิยมวิทยา** (TMD), และ **สทนช.** (ONWR) สายด่วนสาธารณภัยของไทยคือ **1784**
พิมพ์ไว้ข้างคะแนน ในฐานะทางไปหาหน่วยงานเหล่านั้น ตอนเผยแพร่ให้ยืนยันเลขบนหน้าของ ปภ. เอง
อย่าแต่งสายด่วนที่สอง และอย่าให้บัญชีแชทของเทศบาลฟังดูเหมือนออกคำสั่งแล้ว

เมื่อมีคนอยู่ในอันตรายเฉพาะหน้า ประโยคที่มีประโยชน์คือช่องทางทางการ คะแนนของคุณ
อยู่ข้างประโยคนั้นได้ มันไม่ได้แทนที่ประโยคนั้น

---

## 10.4 Dignity beside the map / ศักดิ์ศรีข้างแผนที่

**The job.** Suburban sois and housing estates — ซอย หมู่บ้าน เคหะ — are
where people live. Treat them that way.

**EN.** Believe the person standing next to the map. They can see their
own street. Also believe the gauge about the gauge: a quiet station is
a fact about that station, including its age and its distance from the
door. When the two differ, say both. [Show the disagreement](11-citizen-signals.md).
Do not average it into a depth nobody stated.

Skip pity theatre. Do not narrate a housing estate as a sad backdrop, crop
a street into a photograph with no place name, or speak as if the
suburbs were a case study for the capital. Name the place the way the
people who live there name it. Answer them as neighbours who asked a
practical question.

**TH.** เชื่อคนที่ยืนอยู่ข้างแผนที่ เขาเห็นถนนของตัวเอง และเชื่อมาตรวัดในเรื่องของมาตรวัด:
สถานีที่เงียบคือข้อเท็จจริงของสถานีนั้น รวมอายุของค่าและระยะห่างจากประตูบ้าน
เมื่อสองอย่างไม่ตรงกัน ให้พูดทั้งคู่ [แสดงความไม่ตรงกัน](11-citizen-signals.md)
อย่าเฉลี่ยมันเป็นความลึกที่ไม่มีใครพูดไว้

ข้ามการแสดงความสงสาร อย่าเล่าเคหะเป็นฉากหลังที่น่าสงสาร อย่าตัดถนนเป็นภาพที่ไม่มีชื่อที่
และอย่าพูดราวกับชานเมืองเป็นกรณีศึกษาของเมืองหลวง เรียกชื่อที่แบบที่คนที่อยู่ตรงนั้นเรียก
ตอบเขาในฐานะเพื่อนบ้านที่ถามคำถามที่ใช้การได้

---

## 10.5 Prose a person can forward / ข้อความที่คนส่งต่อได้

**The job.** A short note an aunt can paste into Line and still have it
read as a person.

**EN.** Write plain sentences in one language — the language you are
sending. Three to six lines is enough: place, state, age, source,
official pointer. Then the link, if the link answers (see §10.7).

Chat apps show markdown literally. Asterisks from `**bold**` arrive as
asterisks. Headings, tables, and a stack of badges do not survive the
paste. The bilingual *signage* rule in [§5.2](05-design-language.md)
belongs on the map. A forwarded note is dynamic content: one language,
short, spoken.

Skip the bot voice. No preamble about being a system. No apology for
being a model. No offer to “optimise your situational awareness.” The
note sounds like a careful neighbour who checked a source and is honest
about the gap.

**TH.** เขียนประโยคธรรมดาภาษาเดียว — ภาษาที่คุณกำลังส่ง สามถึงหกบรรทัดพอ:
ที่ สถานะ อายุของค่า ที่มา แล้วทางไปหน่วยงาน จากนั้นคือลิงก์ ถ้าลิงก์ตอบได้ (ดูหัวข้อ 10.7)

แอปแชทแสดงมาร์กดาวน์ตรงตัว ดอกจันจาก `**ตัวหนา**` ไปถึงอีกฝั่งเป็นดอกจัน
หัวข้อ ตาราง และแบดจ์เรียงกันไม่รอดการวาง กฎ *ป้ายสองภาษา* ใน[หัวข้อ 5.2](05-design-language.md)
เป็นของแผนที่ ข้อความที่ส่งต่อเป็นเนื้อหาที่เปลี่ยนได้: ภาษาเดียว สั้น พูดเป็นคน

ข้ามน้ำเสียงบอต ไม่มีเกริ่นว่าเป็นระบบ ไม่มีคำขอโทษว่าเป็นโมเดล ไม่มีข้อเสนอให้
“ยกระดับการรับรู้สถานการณ์” ข้อความควรฟังเหมือนเพื่อนบ้านที่ตรวจแหล่งแล้ว
และซื่อสัตย์กับช่องว่าง

---

## 10.6 Soft-cite: stated centimetres, a locked place, an optional short link / อ้างอย่างนุ่ม: เซนติเมตรที่มีคนพูด, ที่ที่ล็อกไว้, ลิงก์สั้นถ้าจำเป็น

**EN.** Three habits keep a forwarded note from becoming a rumour with a
map attached.

1. **Tip-stated centimetres only.** If a person stated a depth, you may
   repeat that depth and attribute it: who, and when. The verb matters —
   *stated*, *รายงานว่า* — so a tip never wears a gauge’s badge. If they
   gave no number, skip the number. “Knee deep,” “up to the car door,”
   and “a lot” stay in their words. Do not convert a phrase into
   centimetres. Do not invent a depth the tip never contained. The full
   rule for tips is [citizen signals](11-citizen-signals.md).

2. **A locked place link.** A share URL should carry a stable place id
   in a query parameter. The pattern this blueprint names is `?place=`.
   You may use that key or one other single key. Pick it and keep it.
   The id names a soi, a canal, an estate, a station, or a basin. It
   does not carry a water level, a score, or the timestamp of a rumour.
   *Locked* means the same id opens the same place next week. Do not
   renumber places during a flood. A link forwarded yesterday must still
   open the soi it named. Opening it selects that place and shows the
   honest panel: last reading, age, and any disagreement. A national
   overview is the wrong landing page for a soi question.

3. **A short link is optional.** Chat wraps long URLs. A shortener you
   control (bit.ly is only an example of the *kind* of link, not a
   required vendor and not a FloodDash endpoint) can be a courtesy CTA
   to your own free page. The long `?place=` URL is the real link. The
   short one must resolve to the same origin, and that origin must
   answer. If it cannot, send no link.

**TH.** สามนิสัยนี้กันไม่ให้ข้อความที่ส่งต่อกลายเป็นข่าวลือที่มีแผนที่แนบ

1. **เฉพาะเซนติเมตรที่ทิประบุ** ถ้ามีคนระบุความลึก คุณพูดความลึกนั้นซ้ำได้
   พร้อมที่มา: ใคร และเมื่อไร คำกริยาสำคัญ — *stated*, *รายงานว่า* — เพื่อไม่ให้ทิป
   สวมแบดจ์ของมาตรวัด ถ้าเขาไม่ให้ตัวเลข ก็ไม่ต้องมีตัวเลข “ถึงเข่า” “ถึงประตูรถ”
   และ “ท่วมเยอะ” คงไว้ด้วยถ้อยคำของเขา อย่าแปลงวลีเป็นเซนติเมตร อย่าแต่งความลึกที่ทิปไม่มี
   กฎเต็มของทิปอยู่ที่[สัญญาณจากประชาชน](11-citizen-signals.md)

2. **ลิงก์ที่ที่ล็อกไว้** URL ที่แชร์ควรพกหมายเลขที่ที่คงที่ในพารามิเตอร์
   แพทเทิร์นที่พิมพ์เขียวนี้ตั้งชื่อคือ `?place=` คุณจะใช้คีย์นั้นหรือคีย์เดียวอีกคีย์หนึ่งก็ได้
   เลือกแล้วคงไว้ หมายเลขนั้นตั้งชื่อซอย คลอง เคหะ สถานี หรือลุ่มน้ำ มันไม่พกระดับน้ำ
   คะแนน หรือเวลาของข่าวลือ *ล็อก* แปลว่าหมายเลขเดิมเปิดที่เดิมในสัปดาห์หน้า
   อย่าเปลี่ยนเลขที่ระหว่างน้ำท่วม ลิงก์ที่ส่งเมื่อวานต้องยังเปิดซอยที่มันตั้งชื่อ
   การเปิดต้องเลือกที่นั้น และแสดงแผงที่ซื่อสัตย์: ค่าล่าสุด อายุ และความไม่ตรงกันถ้ามี
   ภาพรวมทั้งประเทศเป็นหน้าที่ผิดสำหรับคำถามเรื่องซอย

3. **ลิงก์สั้นเป็นทางเลือก** แชทตัด URL ยาว ตัวย่อที่คุณควบคุมได้ (bit.ly เป็นเพียงตัวอย่างของ
   *ชนิด* ของลิงก์ ไม่ใช่ผู้ให้บริการบังคับ และไม่ใช่เอนด์พอยต์ของ FloodDash) ใช้เป็นคำชวนสั้น ๆ
   ไปหน้าฟรีของคุณได้ URL `?place=` แบบยาวคือลิงก์จริง ลิงก์สั้นต้องชี้มาที่ต้นทางเดียวกัน
   และต้นทางนั้นต้องตอบ ถ้าตอบไม่ได้ อย่าส่งลิงก์

---

## 10.7 Map-believer trust / ความไว้ใจของคนที่เชื่อแผนที่

**EN.** Some people trust the map more than the paragraph. If you invite
a click, they will click. The click is a promise: the origin opens on
that place and answers.

An answer can be a last reading with its age. An answer can be “no
reading since 14:32.” An answer can be the disagreement badge — citizen
media shows water, gauges are quiet — with both sides visible. A blank
map, a spinner, or an error page is not an answer.

Under a share spike, a blank map costs more trust than a missing tip.
Leaving an ungrounded rumour out of the note is a smaller failure than
training a neighbourhood to believe that your links open onto nothing.
The way to keep the promise is operational and civic at once: serve a
light place snapshot while anything heavy warms up
([§8.9](08-lessons-from-production.md)). Do not send the link until you
have opened it yourself, on a phone, on the network a forwarded message
will actually use.

**TH.** บางคนเชื่อแผนที่มากกว่าย่อหน้า ถ้าคุณชวนให้กด เขาจะกด การกดคือคำสัญญา:
ต้นทางเปิดที่นั้น และตอบ

คำตอบอาจเป็นค่าล่าสุดพร้อมอายุ คำตอบอาจเป็น “ไม่มีค่าตั้งแต่ 14:32”
คำตอบอาจเป็นแบดจ์ความไม่ตรงกัน — สื่อจากประชาชนเห็นน้ำ มาตรวัดเงียบ — โดยเห็นทั้งสองฝั่ง
แผนที่ว่าง วงหมุน หรือหน้าข้อผิดพลาด ไม่ใช่คำตอบ

ใต้คลื่นการแชร์ แผนที่ว่างทำลายความไว้ใจมากกว่าทิปที่ไม่ได้ใส่
การเว้นข่าวลือที่ยังไม่ยึดแหล่งออกจากข้อความ เป็นความพลาดที่เล็กกว่าการฝึกทั้งชุมชนให้เชื่อว่า
ลิงก์ของคุณเปิดแล้วเจอความว่าง วิธีรักษาคำสัญญาเป็นทั้งงานระบบและงานพลเมืองพร้อมกัน:
เสิร์ฟภาพสรุปของที่แบบเบา ขณะที่งานหนักยังอุ่นเครื่อง
([หัวข้อ 8.9](08-lessons-from-production.md)) อย่าส่งลิงก์จนกว่าคุณจะเปิดมันเอง
บนมือถือ บนเครือข่ายที่ข้อความส่งต่อจะใช้จริง

---

## 10.8 Muni Q&A paste pack / ชุดคำตอบเทศบาลสำหรับวางส่ง

**EN.** This is a **method template**. Fields only. It is not FloodDash
automation, not a script, not a prompt, and not a licence to let a model
fill the blanks with water levels. A person fills it from sources they
can name. Then they paste **plain text** into chat — no asterisks, no
headings.

Use one language per send. Keep the website’s signage bilingual
elsewhere. If a field has nothing honest to say, leave the field out.
An empty depth is better than a guessed depth.

The partner-credit field names whoever supported *this* page, if anyone.
Credit is for the illustration or the municipal effort. It is not an
author line for an evacuation order, and it is not a claim that a sponsor
owns this blueprint. The live illustration’s public credits are listed
in the [README](../README.md), separate from this empty template.

**TH.** นี่คือ **แม่แบบวิธี** มีแต่ช่อง มันไม่ใช่ระบบอัตโนมัติของ FloodDash
ไม่ใช่สคริปต์ ไม่ใช่พรอมป์ต์ และไม่ใช่ใบอนุญาตให้โมเดลเติมช่องด้วยระดับน้ำ
คนเป็นคนเติมจากแหล่งที่ระบุชื่อได้ แล้ววางเป็น **ข้อความธรรมดา** ในแชท —
ไม่มีดอกจัน ไม่มีหัวข้อ

หนึ่งภาษาต่อการส่งหนึ่งครั้ง ป้ายสองภาษาของเว็บอยู่ที่อื่น ถ้าช่องไหนไม่มีอะไรซื่อสัตย์จะพูด
ก็ตัดช่องนั้นทิ้ง ความลึกที่ว่างดีกว่าความลึกที่เดา

ช่องเครดิตพันธมิตรระบุผู้ที่สนับสนุน *หน้านี้* ถ้ามี เครดิตเป็นของภาพประกอบหรือความพยายามของเทศบาล
มันไม่ใช่บรรทัดผู้จัดทำคำสั่งอพยพ และไม่ใช่การอ้างว่าผู้สนับสนุนเป็นเจ้าของพิมพ์เขียวนี้
เครดิตสาธารณะของภาพประกอบจริงอยู่ใน [README](../README.md) แยกจากแม่แบบว่างนี้

```text
MUNI Q&A PASTE PACK  ·  ชุดคำตอบเทศบาล
METHOD TEMPLATE — fields only. Not FloodDash automation.
แม่แบบวิธี — มีแต่ช่อง ไม่ใช่ระบบอัตโนมัติของ FloodDash
Fill by hand. Paste as plain text. One language per send.
เติมด้วยมือ วางเป็นข้อความธรรมดา ส่งครั้งละหนึ่งภาษา

[ANSWER · คำตอบ]
<this place, this hour, in one or two sentences>

[TIP / DASH HONESTY · ความซื่อสัตย์ของทิปกับแดช]
<what a person stated, and when — centimetres only if they stated a number>
<what the gauges say, and how old they are>
<if the two disagree, say they disagree; do not average them>

[SCORE ≠ อพยพ]
<watch score = where to look; evacuation belongs to DDPM / TMD / ONWR>

[PLACE LINK · ลิงก์ที่]
<your origin + ?place= a stable id for this soi / canal / estate>
<send only if you have opened it and the origin answers>

[1784]
<public disaster hotline; confirm on DDPM’s own page before you publish>

[FREE WEB CTA · ชวนเปิดหน้าฟรี]
<optional short link to YOUR free page; omit if the page cannot answer>

[PARTNER CREDIT · เครดิตพันธมิตร]
<who supported this page, if anyone — sponsors of an illustration, not authors of an order>
```

---

[← Compute kit](09-compute-and-data.md) · [Next: Citizen signals →](11-citizen-signals.md)
