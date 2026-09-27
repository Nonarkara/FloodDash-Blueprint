<!--
SPDX-FileCopyrightText: 2026 Dr Non Arkaraprasertkul
SPDX-License-Identifier: CC-BY-4.0
-->

# 11. Citizen signals / สัญญาณจากประชาชน

[← Civic jobs and trust](10-civic-jobs-and-trust.md) · [Next: First hour after clone →](12-first-hour-fork.md)

---

**EN.** A neighbour who can see the water is a sensor the national gauge
network does not replace. Treat the tip as a **first-class signal**:
store it, time it, credit it, expire it, and show it beside the gauges.
Do not treat it as a rumour to amplify, and do not treat it as a number
your dashboard is allowed to invent.

This page is an editorial and data-handling method. The names below are
**ways of speaking and labelling**. They are not product names, not
module names, and not a map of anyone’s private code. Nothing here is
permission to scrape [flood.nonarkara.org](https://flood.nonarkara.org)
or to reconstruct the private twin.

**TH.** เพื่อนบ้านที่มองเห็นน้ำคือเซนเซอร์ที่เครือข่ายมาตรวัดของประเทศแทนที่ไม่หมด
ถือทิปเป็น **สัญญาณชั้นหนึ่ง**: เก็บ ลงเวลา ให้เครดิต หมดอายุ และแสดงข้างมาตรวัด
อย่าถือว่าเป็นข่าวลือให้ขยาย และอย่าถือว่าเป็นตัวเลขที่แดชบอร์ดของคุณมีสิทธิ์แต่ง

หน้านี้เป็นวิธีบรรณาธิการและวิธีจัดการข้อมูล ชื่อด้านล่างเป็น **วิธีพูดและวิธีติดป้าย**
ไม่ใช่ชื่อผลิตภัณฑ์ ไม่ใช่ชื่อโมดูล และไม่ใช่แผนที่ของโค้ดส่วนตัวของใคร ไม่มีอะไรในนี้ที่เป็น
สิทธิ์ให้ขูด [flood.nonarkara.org](https://flood.nonarkara.org) หรือรื้อคู่แฝดส่วนตัว

The speaking habits that carry a tip into Line live in
[civic jobs](10-civic-jobs-and-trust.md). The rule that a language model
may not invent a reading lives here, in [§7](07-honest-limitations.md),
and in [§8.6](08-lessons-from-production.md).

---

## 11.1 Tip-stated depth only / เฉพาะความลึกที่ทิประบุ

**EN.** Repeat a centimetre only when the tip itself stated that
centimetre. Keep the attribution in the same sentence: who said it, and
at what time. A tip-stated depth wears a tip badge for its whole life.
It never graduates into a gauge reading because it was forwarded many
times.

If the tip has no number, skip the number. Keep the words they used.
“ถึงเข่า”, “to the wheel”, “across the soi” are observations in language.
They are not a conversion table. A lookup that turns a phrase into
centimetres has invented a depth. Do not ship it.

Skip the tip’s *depth field* when there is no number. You may still
record that a person reported water, with the time and the place, and
with no centimetre attached.

**TH.** พูดเซนติเมตรซ้ำได้เฉพาะเมื่อทิปเองระบุเซนติเมตรนั้น ให้เครดิตในประโยคเดียวกัน:
ใครพูด และเมื่อไร ความลึกที่ทิประบุใส่แบดจ์ทิปตลอดอายุของมัน มันไม่เลื่อนชั้นเป็นค่ามาตรวัด
เพราะถูกส่งต่อหลายครั้ง

ถ้าทิปไม่มีตัวเลข ก็ไม่ต้องมีตัวเลข คงถ้อยคำที่เขาใช้ “ถึงเข่า” “ถึงล้อ” “ท่วมข้ามซอย”
เป็นการสังเกตในภาษา ไม่ใช่ตารางแปลง การเปิดตารางแปลงวลีเป็นเซนติเมตร
คือการแต่งความลึก อย่าส่งสิ่งนั้นออกไป

ข้าม *ช่องความลึก* ของทิปเมื่อไม่มีตัวเลข คุณยังบันทึกได้ว่ามีคนรายงานว่ามีน้ำ
พร้อมเวลาและสถานที่ โดยไม่แนบเซนติเมตร

---

## 11.2 Show disagreement; do not average it / แสดงความไม่ตรงกัน อย่าเฉลี่ยทิ้ง

**EN.** Citizen media and gauges will disagree. A photo can show water
on a soi while the nearest station is quiet, stale, or on a different
canal. The honest move is a **badge that states the disagreement**, in
the same visual grammar as the rest of your status badges
([§5.1](05-design-language.md)): shape plus words, not colour alone.

A workable pair of labels, for you to translate into your own UI:

| Situation | Badge, Thai | Badge, English |
|---|---|---|
| Citizen media shows water; gauges are quiet or stale | ทิปมีน้ำ · มาตรวัดเงียบ | tip wet · gauges quiet |
| Tip and gauges describe the same kind of state | ทิปกับมาตรวัดตรงกัน | tip and gauges agree |

“The same kind of state” means both say water is present, or both say
it is quiet. It does not mean you have computed a shared centimetre.

Put both facts on the place panel: the tip, with its time and credit,
and the gauge, with its station name and age. A reader should see the
tension in one glance. Averaging, interpolating, or “splitting the
difference” deletes the tension and fabricates a third reading that
nobody measured. That third reading is the rumour.

The quiet gauge remains in the dataset. The tip remains a tip. The
badge is how you refuse to pretend they are one instrument.

**TH.** สื่อจากประชาชนกับมาตรวัดจะไม่ตรงกัน ภาพอาจเห็นน้ำบนซอย ขณะที่สถานีใกล้สุด
เงียบ ค่าเก่า หรืออยู่คนละคลอง ท่าทีที่ซื่อสัตย์คือ **แบดจ์ที่บอกความไม่ตรงกัน**
ด้วยไวยากรณ์ภาพเดียวกับแบดจ์สถานะที่เหลือ ([หัวข้อ 5.1](05-design-language.md)):
รูปทรงคู่กับคำ ไม่ใช่สีอย่างเดียว

คู่ป้ายที่ใช้ได้ ให้คุณแปลเข้า UI ของตัวเอง:

| สถานการณ์ | ป้ายไทย | ป้ายอังกฤษ |
|---|---|---|
| สื่อจากประชาชนเห็นน้ำ มาตรวัดเงียบหรือค่าเก่า | ทิปมีน้ำ · มาตรวัดเงียบ | tip wet · gauges quiet |
| ทิปกับมาตรวัดบรรยายสถานะชนิดเดียวกัน | ทิปกับมาตรวัดตรงกัน | tip and gauges agree |

“สถานะชนิดเดียวกัน” หมายถึงทั้งคู่บอกว่ามีน้ำ หรือทั้งคู่บอกว่าเงียบ
ไม่ได้หมายถึงคุณคำนวณเซนติเมตรร่วมกันได้แล้ว

วางทั้งสองข้อเท็จจริงบนแผงของที่: ทิป พร้อมเวลาและเครดิต และมาตรวัด พร้อมชื่อสถานีและอายุ
ผู้อ่านควรเห็นความตึงในพริบตา การเฉลี่ย การคาดช่วง หรือการ “หารครึ่งความต่าง”
ลบความตึงนั้น และสร้างค่าที่สามที่ไม่มีใครวัด ค่านั้นคือข่าวลือ

มาตรวัดที่เงียบยังอยู่ในชุดข้อมูล ทิปยังเป็นทิป แบดจ์คือวิธีที่คุณไม่แกล้งว่าทั้งคู่เป็นเครื่องมือเดียวกัน

---

## 11.3 Two editorial patterns / สองแพทเทิร์นบรรณาธิการ

**EN.** When you speak in public — a paste into Line, a caption, a
briefing — use one of two **honesty patterns**. Call them Path A and
Path B if that helps your editors share a vocabulary. The names are a
shared habit. They are not features you should go looking for in
private source, because that source is not here.

**Path A — agency or news.** You are speaking from a gauge, a dam
release, a forecast, or a news report that itself cites one of those.
Name the agency or the outlet, the time, and the age. Keep the
[measured / modelled / seasonal](04-the-science.md) badge on whatever
you cited. A news article is not a station. A forecast is not a level.
Path A is allowed to sound definite only about what that source
actually said.

**Path B — tip and dashboard agree.** You may say the tip and the
dashboard agree when they describe the same kind of state, as in the
second row of §11.2. Still say which sentence is the neighbour and
which sentence is the gauge. Agreement is not a new instrument and not
a blended depth. If you have a tip-stated centimetre, it stays in the
tip sentence. The gauge sentence keeps the gauge’s own units and time.

When they do **not** agree, you do not have a Path B sentence. You have
the disagreement badge. Speak both facts. Produce no compromise
centimetre. Silence on the depth is the honest product.

A third temptation — a model that “reconciles” the photo and the gauge
into one level — is out of both paths. It is the failure mode in §11.5.

**TH.** เมื่อพูดต่อสาธารณะ — วางในไลน์ คำบรรยายภาพ ห้องบรีฟ — ให้ใช้หนึ่งในสอง
**แพทเทิร์นความซื่อสัตย์** จะเรียกว่าเส้นทาง A และเส้นทาง B ก็ได้ ถ้าช่วยให้บรรณาธิการใช้คำร่วมกัน
ชื่อเหล่านี้เป็นนิสัยร่วม ไม่ใช่ฟีเจอร์ที่คุณควรไปหาในซอร์สส่วนตัว เพราะซอร์สนั้นไม่ได้อยู่ที่นี่

**เส้นทาง A — หน่วยงานหรือข่าว** คุณกำลังพูดจากมาตรวัด การระบายเขื่อน พยากรณ์
หรือข่าวที่อ้างสิ่งเหล่านั้นเอง ระบุหน่วยงานหรือสำนักข่าว เวลา และอายุของค่า
คงแบดจ์[วัดได้ / จำลอง / ตามฤดูกาล](04-the-science.md) ไว้บนสิ่งที่คุณอ้าง
บทข่าวไม่ใช่สถานี พยากรณ์ไม่ใช่ระดับน้ำ เส้นทาง A พูดได้ชัดเฉพาะสิ่งที่แหล่งนั้นพูดจริง

**เส้นทาง B — ทิปกับแดชบอร์ดตรงกัน** คุณพูดได้ว่าทิปกับแดชบอร์ดตรงกัน เมื่อทั้งคู่บรรยาย
สถานะชนิดเดียวกัน ตามแถวที่สองของหัวข้อ 11.2 และยังต้องบอกว่าประโยคไหนเป็นของเพื่อนบ้าน
ประโยคไหนเป็นของมาตรวัด ความตรงกันไม่ใช่เครื่องมือใหม่ และไม่ใช่ความลึกที่ผสม
ถ้ามีเซนติเมตรที่ทิประบุ มันอยู่ในประโยคทิป ประโยคมาตรวัดคงหน่วยและเวลาของมาตรวัดเอง

เมื่อทั้งคู่ **ไม่** ตรงกัน คุณไม่มีประโยคเส้นทาง B คุณมีแบดจ์ความไม่ตรงกัน พูดทั้งสองข้อเท็จจริง
อย่าผลิตเซนติเมตรประนีประนอม ความเงียบเรื่องความลึกคือผลงานที่ซื่อสัตย์

สิ่งล่อใจที่สาม — โมเดลที่ “กลบเกลื่อน” ภาพกับมาตรวัดให้เป็นระดับเดียว — อยู่นอกทั้งสองเส้นทาง
มันคือความล้มเหลวในหัวข้อ 11.5

---

## 11.4 Consent, credit, and live expiry / ความยินยอม เครดิต และอายุของคำว่า “สด”

**EN.** A tip is someone’s words and sometimes their photograph or clip.
Publish it in public only under a consent practice you can explain in a
sentence: they handed it to your public desk for the watch, or a
newsroom already published it and you are citing that publication.
A private chat, a closed group, or a neighbour’s camera roll is not a
feed. Do not join private rooms to harvest them, and do not paste those
rooms into this blueprint or into yours.

**Credit** is one line. Name the person as they asked to be named, or
write that a neighbour asked not to be named. If the source is a news
outlet, name the outlet and link it. Credit is how a tip stays
distinguishable from a gauge.

**Live expiry** is a window you choose and print. Water moves. A tip
with no time is not live; file it as undated and keep it off the “now”
panel. A tip inside the window can wear a live badge next to its clock.
When the window closes, the same tip becomes history: still visible in
a log if you keep one, no longer offered as the current depth of the
soi. Say the window in the methodology page, in both languages — for
example a few hours — and keep the number you actually enforce. Taking
a photo down when the person asks is part of the same practice. A credit
line you cannot retract is a good reason to have asked first.

Do not send someone’s photo or private wording to a third-party model
unless that forwarding was part of the consent.

**TH.** ทิปคือถ้อยคำของใครคนหนึ่ง และบางครั้งคือภาพหรือคลิปของเขา เผยแพร่สู่สาธารณะได้
ภายใต้แนวปฏิบัติความยินยอมที่อธิบายได้ในหนึ่งประโยค: เขาส่งให้โต๊ะสาธารณะของคุณเพื่อการเฝ้าระวัง
หรือสำนักข่าวเผยแพร่แล้วและคุณกำลังอ้างสิ่งพิมพ์นั้น แชทส่วนตัว กลุ่มปิด หรือม้วนภาพของเพื่อนบ้าน
ไม่ใช่ฟีด อย่าเข้าห้องส่วนตัวเพื่อเก็บเกี่ยว และอย่าวางห้องเหล่านั้นลงในพิมพ์เขียวนี้หรือของคุณ

**เครดิต** คือหนึ่งบรรทัด เรียกชื่อคนแบบที่เขาขอให้เรียก หรือเขียนว่าเพื่อนบ้านขอไม่ระบุชื่อ
ถ้าแหล่งเป็นสำนักข่าว ให้ชื่อสำนักข่าวและใส่ลิงก์ เครดิตคือสิ่งที่ทำให้ทิปยังแยกจากมาตรวัดได้

**อายุของคำว่าสด** คือหน้าต่างที่คุณเลือกและพิมพ์ไว้ น้ำเคลื่อน ทิปที่ไม่มีเวลาไม่ใช่ของสด
เก็บเป็นไม่มีวันที่ และกันออกจากแผง “ตอนนี้” ทิปที่อยู่ในหน้าต่างใส่แบดจ์สดข้างนาฬิกาของมันได้
เมื่อหน้าต่างปิด ทิปเดียวกันกลายเป็นประวัติ: ยังเห็นในบันทึกถ้าคุณเก็บ และไม่ถูกเสนอเป็นความลึกปัจจุบันของซอย
บอกหน้าต่างนั้นในหน้าวิธีการ ทั้งสองภาษา — เช่น ไม่กี่ชั่วโมง — แล้วคงตัวเลขที่คุณบังคับใช้จริง
การนำภาพลงเมื่อเจ้าของขอ เป็นส่วนหนึ่งของแนวปฏิบัติเดียวกัน บรรทัดเครดิตที่ถอนไม่ได้
เป็นเหตุผลที่ดีที่จะถามก่อน

อย่าส่งภาพหรือถ้อยคำส่วนตัวของใครไปยังโมเดลของบุคคลที่สาม เว้นแต่การส่งต่อนั้นอยู่ในความยินยอม

---

## 11.5 Never invent centimetres / อย่าแต่งเซนติเมตร

**EN.** This is the same ethical floor as the rest of the blueprint,
applied to the signal people most want a model to “help” with.

A language model may shorten a sentence a person already wrote, in
which every number was already present and attributed. It may not add a
depth, a time, a station, a score, or a place. If a draft contains a
figure that was not in the grounded text, discard the draft. Do not
edit the figure down to something “more plausible.” Plausible is how
invented water sounds.

The model does not get a special case for citizen tips. “The photo
looks about this deep” is an invented reading if nobody stated a
number. So is a tool that fills a missing gauge from nearby stations
and labels the result as measured. Gaps stay gaps. The badge in §11.2
is what you show instead.

Operators fill the [paste pack](10-civic-jobs-and-trust.md) by hand
from Path A or Path B. The pack is not a prompt.

**TH.** นี่คือพื้นจริยธรรมเดียวกับส่วนที่เหลือของพิมพ์เขียว ใช้กับสัญญาณที่คนอยากให้โมเดล
“ช่วย” ที่สุด

โมเดลภาษาช่วยย่อประโยคที่คนเขียนไว้แล้วได้ ในประโยคที่ตัวเลขทุกตัวมีอยู่และมีที่มาแล้ว
มันเพิ่มความลึก เวลา สถานี คะแนน หรือสถานที่ไม่ได้ ถ้าฉบับร่างมีตัวเลขที่ไม่อยู่ในข้อความที่ยึดแหล่ง
ให้ทิ้งฉบับร่าง อย่าแก้ตัวเลขให้ “น่าจะเป็น” ไปมากกว่านี้ ความน่าจะเป็นคือเสียงของน้ำที่ถูกแต่ง

โมเดลไม่ได้ข้อยกเว้นสำหรับทิปประชาชน “ภาพดูลึกประมาณนี้” คือค่าที่แต่งขึ้น ถ้าไม่มีใครระบุตัวเลข
เช่นเดียวกับเครื่องมือที่เติมมาตรวัดที่หายจากสถานีข้างเคียงแล้วติดป้ายว่าเป็นค่าที่วัดได้
ช่องว่างต้องยังเป็นช่องว่าง แบดจ์ในหัวข้อ 11.2 คือสิ่งที่คุณแสดงแทน

ผู้ปฏิบัติการเติม[ชุดคำตอบ](10-civic-jobs-and-trust.md) ด้วยมือ จากเส้นทาง A หรือเส้นทาง B
ชุดคำตอบไม่ใช่พรอมป์ต์

---

[← Civic jobs and trust](10-civic-jobs-and-trust.md) · [Next: First hour after clone →](12-first-hour-fork.md)
