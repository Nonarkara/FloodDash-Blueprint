<!--
SPDX-FileCopyrightText: 2026 Dr Non Arkaraprasertkul
SPDX-License-Identifier: CC-BY-4.0
-->

# 12. The first hour after clone / ชั่วโมงแรกหลัง clone

[← Citizen signals](11-citizen-signals.md) · [Back to README](../README.md)

---

**EN.** You cloned a blueprint. This hour is for decisions a flood
operator should make before a map exists: what you will not claim, which
single public feed you will prove, how measured and modelled values will
be labelled, and where the watch-score disclaimer will sit. It is not
Phases 0–5. Those still take the time in
[the roadmap](06-build-your-own-roadmap.md).

**TH.** คุณ clone พิมพ์เขียวมา ชั่วโมงนี้ไว้สำหรับการตัดสินใจที่ผู้ดูแลน้ำท่วมควรทำ
ก่อนมีแผนที่: อะไรที่คุณจะไม่อ้าง ฟีดสาธารณะเดียวที่คุณจะพิสูจน์ ค่าที่วัดได้กับค่าที่จำลอง
จะติดป้ายอย่างไร และข้อความปฏิเสธความของคะแนนเฝ้าระวังจะอยู่ที่ไหน นี่ไม่ใช่เฟส 0–5
เฟสเหล่านั้นยังใช้เวลาตาม[แผนสร้าง](06-build-your-own-roadmap.md)

There is still no `src/` in this repository. FloodDash is not open
source. [flood.nonarkara.org](https://flood.nonarkara.org) is an
illustration. Do not scrape it, do not point adapters at it, and do not
treat it as a staging API.

ใน repository นี้ยังไม่มี `src/` FloodDash ไม่ใช่โอเพนซอร์ส
[flood.nonarkara.org](https://flood.nonarkara.org) เป็นภาพประกอบ อย่าขูด
อย่าชี้ adapter ไปที่นั่น และอย่าถือว่าเป็น API สเตจ

---

## The hour, in order / ชั่วโมงนี้ ตามลำดับ

### 1. Ethics, before tools / จริยธรรม ก่อนเครื่องมือ

**EN.** Read [Ethical use](../README.md#ethical-use--การใช้ตามจริยธรรม) and
[honest limitations](07-honest-limitations.md). Write down four refusals
you will keep when someone asks for a shortcut:

- You will reconstruct from `docs/`, not from the running building.
- You will not say FloodDash is open source because this blueprint exists.
- You will not present a watch score as an official warning. Evacuation
  stays with **DDPM / TMD / ONWR**. The public disaster hotline to print
  beside that sentence is **1784**, confirmed on DDPM’s own page.
- You will not invent a reading when a station, a tip, or a model is silent.

**TH.** อ่าน[การใช้ตามจริยธรรม](../README.md#ethical-use--การใช้ตามจริยธรรม) และ
[ข้อจำกัดอย่างซื่อสัตย์](07-honest-limitations.md) จดการปฏิเสธสี่ข้อที่คุณจะรักษาไว้
เมื่อมีคนขอทางลัด:

- คุณจะสร้างใหม่จาก `docs/` ไม่ใช่จากอาคารที่กำลังใช้งาน
- คุณจะไม่พูดว่า FloodDash เป็นโอเพนซอร์สเพราะมีพิมพ์เขียวนี้
- คุณจะไม่เสนอคะแนนเฝ้าระวังเป็นประกาศเตือนภัยทางการ การอพยพอยู่ที่ **ปภ. / กรมอุตุฯ / สทนช.**
  สายด่วนสาธารณภัยที่จะพิมพ์ข้างประโยคนั้นคือ **1784** โดยยืนยันบนหน้าของ ปภ. เอง
- คุณจะไม่แต่งค่าที่วัดได้ เมื่อสถานี ทิป หรือแบบจำลองเงียบ

### 2. One public feed — Phase 0, started honestly / ฟีดสาธารณะเดียว — เฟส 0 เริ่มอย่างตรงไปตรงมา

**EN.** Pick **one** feed from [the catalog](03-data-sources.md). A
national water-level feed is the richest first signal. In this hour,
read its quirks and start the loop the roadmap already defines: fetch,
validate (dates, types, sentinels such as `-1`), store with the
uniqueness rule in [§2.3](02-architecture.md).

Done, when you get there, means two poll cycles, real timestamps, and
zero duplicate rows. No UI yet. The hour is not a promise that the loop
is finished. It is a promise that you did not open a map library first.

Compute from the public URLs in [the compute kit](09-compute-and-data.md).
Env **names** can be copied. Env **values** cannot, because this repo
does not have them.

**TH.** เลือกฟีด **เดียว** จาก[แคตตาล็อก](03-data-sources.md) ฟีดระดับน้ำระดับประเทศ
เป็นสัญญาณแรกที่อุดมที่สุด ในชั่วโมงนี้ ให้อ่านข้อควรระวังของมัน แล้วเริ่มวงจรที่แผนงานนิยามไว้แล้ว:
ดึง ตรวจ (วันที่ ชนิด ค่า sentinel เช่น `-1`) เก็บด้วยกฎไม่ซ้ำใน[หัวข้อ 2.3](02-architecture.md)

คำว่าเสร็จ เมื่อคุณไปถึง จุดนั้นคือสองรอบการดึง timestamp จริง และไม่มีแถวซ้ำ ยังไม่ทำ UI
ชั่วโมงนี้ไม่ใช่คำสัญญาว่าวงจรเสร็จ มันเป็นคำสัญญาว่าคุณไม่ได้เปิดไลบรารีแผนที่ก่อน

คำนวณจาก URL สาธารณะใน[ชุดคำนวณ](09-compute-and-data.md) **ชื่อ** ตัวแปรสภาพแวดล้อมคัดลอกได้
**ค่า** คัดลอกไม่ได้ เพราะ repository นี้ไม่มีค่าเหล่านั้น

### 3. Measured and modelled, labelled before they share a screen / ของที่วัดได้กับของที่จำลอง ติดป้ายก่อนอยู่จอเดียวกัน

**EN.** Decide the four words your UI will use, in both languages, before
any layer is pretty:

| Kind | What it is allowed to say |
|---|---|
| **Measured** · ค่าวัด | A station or gauge reported this, at this time |
| **Derived** · คำนวณจากของวัด | You computed this from stored readings (watch score, rise rate, soil memory) |
| **Modelled** · แบบจำลอง | A forecast, a gridded model, or a satellite analysis produced this |
| **Unavailable** · ไม่มีค่า | Silence, with the time of the last real report |

Earth-observation overlays are not river gauges. GISTDA flood tiles and
NASA GIBS imagery are named in [§3](03-data-sources.md) and
[§9.3](09-compute-and-data.md). Attribute GISTDA on Thai disaster layers.
Label automated flood detection as not yet verified. Label GIBS as
observed imagery. A seasonal index such as ENSO stays a seasonal prior.

For overlays — GISTDA’s published Open API, NASA GIBS, and the rest of
a forkable EO desk — use the companion toolkit, not this repo and not
the private twin:

[DrNon Global Satellite Toolkit](https://github.com/Nonarkara/DrNon-Global-Satellite-Toolkit)

That repository is an independent open toolkit by the same author. It
is not FloodDash source, not an official GISTDA product, and not an
official warning. Clone it when you want satellite layers. Keep this
blueprint as the method for the flood watch itself.

**TH.** ตัดสินสี่คำที่ UI ของคุณจะใช้ ทั้งสองภาษา ก่อนที่ชั้นข้อมูลใดจะสวย:

| ชนิด | สิ่งที่มันพูดได้ |
|---|---|
| **ค่าวัด** · Measured | สถานีหรือมาตรวัดรายงานค่านี้ ณ เวลานี้ |
| **คำนวณจากของวัด** · Derived | คุณคำนวณสิ่งนี้จากค่าที่เก็บไว้ (คะแนนเฝ้าระวัง อัตราเพิ่มระดับ ความจำของดิน) |
| **แบบจำลอง** · Modelled | พยากรณ์ โมเดลกริด หรือการวิเคราะห์จากดาวเทียมผลิตสิ่งนี้ |
| **ไม่มีค่า** · Unavailable | ความเงียบ พร้อมเวลาของรายงานจริงครั้งล่าสุด |

ชั้นสำรวจโลกไม่ใช่มาตรวัดลำน้ำ ไทล์น้ำท่วมของ GISTDA และภาพ NASA GIBS ถูกตั้งชื่อไว้ใน
[หัวข้อ 3](03-data-sources.md) และ[หัวข้อ 9.3](09-compute-and-data.md) อ้างอิง GISTDA
บนชั้นภัยพิบัติของไทย ติดป้ายการตรวจจับน้ำท่วมอัตโนมัติว่ายังไม่ได้รับการยืนยัน
ติดป้าย GIBS ว่าเป็นภาพที่สังเกตได้ ดัชนีตามฤดูกาลเช่น ENSO ยังเป็นปัจจัยก่อนเหตุ

สำหรับชั้นซ้อน — Open API ที่ GISTDA เผยแพร่, NASA GIBS, และโต๊ะ EO ที่ fork ได้ —
ใช้ชุดเครื่องมือคู่ขนาน ไม่ใช่ repo นี้ และไม่ใช่คู่แฝดส่วนตัว:

[DrNon Global Satellite Toolkit](https://github.com/Nonarkara/DrNon-Global-Satellite-Toolkit)

repository นั้นเป็นชุดเครื่องมือเปิดอิสระของผู้เขียนคนเดียวกัน มันไม่ใช่ซอร์สของ FloodDash
ไม่ใช่ผลิตภัณฑ์ทางการของ GISTDA และไม่ใช่ประกาศเตือนภัยทางการ clone เมื่อคุณต้องการชั้นดาวเทียม
คงพิมพ์เขียวนี้ไว้เป็นวิธีของระบบเฝ้าระวังน้ำท่วมเอง

### 4. Where the watch-score disclaimer sits / ข้อความของคะแนนเฝ้าระวังอยู่ที่ไหน

**EN.** Write the sentence now, from [§4.1](04-the-science.md), in both
languages. You do not need a score yet. You need a place for the sentence
so it cannot be “added later” after people have already forwarded a naked
number.

Put it on:

- every screen where a watch score appears, including a phone in sunlight
- every chat answer that mentions a score
- every export and every briefing slide
- the [muni paste pack](10-civic-jobs-and-trust.md), in the field that
  says a score is not an evacuation

The disclaimer sits in the same view as the number. A methodology page
that nobody opens during a flood does not carry it alone. Next to the
sentence, point to DDPM, TMD, and ONWR, and to **1784**.

**TH.** เขียนประโยคตอนนี้ จาก[หัวข้อ 4.1](04-the-science.md) ทั้งสองภาษา
คุณยังไม่ต้องมีคะแนน คุณต้องมีที่สำหรับประโยค เพื่อไม่ให้มันกลายเป็นสิ่งที่ “ค่อยใส่ทีหลัง”
หลังจากคนส่งต่อตัวเลขเปล่าไปแล้ว

วางมันบน:

- ทุกหน้าจอที่คะแนนเฝ้าระวังปรากฏ รวมมือถือในแดด
- ทุกคำตอบแชทที่พูดถึงคะแนน
- ทุกไฟล์ส่งออกและทุกสไลด์บรีฟ
- [ชุดคำตอบเทศบาล](10-civic-jobs-and-trust.md) ในช่องที่บอกว่าคะแนนไม่ใช่การอพยพ

ข้อความนี้อยู่ในมุมมองเดียวกับตัวเลข หน้าวิธีการที่ไม่มีใครเปิดระหว่างน้ำท่วม
แบกมันลำพังไม่ได้ ข้างประโยค ให้ชี้ไป ปภ. กรมอุตุฯ และ สทนช. และไปที่ **1784**

### 5. If you will speak to a person before the pipes are finished / ถ้าคุณจะพูดกับคนก่อนท่อจะเสร็จ

**EN.** Read [civic jobs and trust](10-civic-jobs-and-trust.md) and
[citizen signals](11-citizen-signals.md) before you send a link. Answer
the place first. State centimetres only when a tip stated them. If you
invite a click, the origin must answer. A blank map under a forwarded
link spends trust you cannot reprint.

Ops lessons for the hour a link actually spreads — cold start, light
snapshots, disk, browser opt-outs, a deliberate second API hostname —
are in [§8.9](08-lessons-from-production.md). They are still reconstructible
without private source.

**TH.** อ่าน[งานที่พลเมืองต้องการและความไว้ใจ](10-civic-jobs-and-trust.md) และ
[สัญญาณจากประชาชน](11-citizen-signals.md) ก่อนส่งลิงก์ ตอบที่ก่อน ระบุเซนติเมตรเฉพาะเมื่อทิประบุ
ถ้าคุณชวนให้กด ต้นทางต้องตอบ แผนที่ว่างใต้ลิงก์ที่ถูกส่งต่อ ใช้ความไว้ใจที่พิมพ์ซ้ำไม่ได้

บทเรียนปฏิบัติการสำหรับชั่วโมงที่ลิงก์แพร่จริง — การสตาร์ตขณะเย็น สแนปชอตเบา ดิสก์
การปฏิเสธของเบราว์เซอร์ ชื่อโฮสต์ API ที่สองซึ่งตั้งใจ — อยู่ใน[หัวข้อ 8.9](08-lessons-from-production.md)
ยังสร้างใหม่ได้โดยไม่มีซอร์สส่วนตัว

---

## Checklist / รายการตรวจ

- [ ] Ethical use read · อ่านการใช้ตามจริยธรรมแล้ว และจดการปฏิเสธสี่ข้อแล้ว
- [ ] One public feed chosen · เลือกฟีดสาธารณะเดียวแล้ว เริ่มวงจรเฟส 0 ยังไม่ทำ UI
- [ ] Four labels chosen, both languages · เลือกสี่ป้ายแล้ว ทั้งสองภาษา: วัดได้, คำนวณ, จำลอง, ไม่มีค่า
- [ ] EO overlays, if any, attributed and pointed at the [satellite toolkit](https://github.com/Nonarkara/DrNon-Global-Satellite-Toolkit) · ชั้นดาวเทียมถ้ามี ต้องอ้างอิง และชี้ไปที่ชุดเครื่องมือดาวเทียม ไม่ใช่ไปที่ FloodDash
- [ ] Watch-score disclaimer drafted in both languages, and the screens that must carry it listed · ร่างข้อความของคะแนนแล้วทั้งสองภาษา และลิสต์หน้าจอที่ต้องติดมัน
- [ ] 1784 and DDPM / TMD / ONWR named next to that disclaimer; number checked on the agency page · ระบุ 1784 และ ปภ. / กรมอุตุฯ / สทนช. ข้างข้อความนั้น และตรวจเลขบนหน้าหน่วยงานแล้ว
- [ ] Adapters point at public agency URLs · adapter ชี้ไป URL สาธารณะของหน่วยงาน ภาพประกอบ [flood.nonarkara.org](https://flood.nonarkara.org) ไม่ได้อยู่ในรายการนั้น
- [ ] If a human will receive a message today: docs 10 and 11 read, and every centimetre is one somebody stated · ถ้าวันนี้มีคนจะได้รับข้อความ: อ่านเอกสาร 10 และ 11 แล้ว และเซนติเมตรทุกตัวมีคนระบุไว้

---

[← Citizen signals](11-citizen-signals.md) · [Back to README](../README.md)
