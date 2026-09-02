# 4. The science: watch scores, connected waterways, and soil memory
## วิทยาศาสตร์: คะแนนเฝ้าระวัง เส้นทางน้ำเชื่อมโยง และความจำของดิน

[← Data Sources](03-data-sources.md) · [Next: Design Language →](05-design-language.md)

---

## 4.1 A province watch score, honestly framed / คะแนนเฝ้าระวังจังหวัด อย่างตรงไปตรงมา

**EN.** The single most important sentence in this entire document is this:
**a watch score computed from live telemetry is a heuristic indicator, not a
flood forecast, and your UI must say so on every screen where the score
appears.** With that established, here is a workable formula shape:

```
score = w1·water + w2·rain + w3·forecast + w4·soil_wetness + w5·rise_rate
```

Wiring from named public feeds, badges (measured / derived / modelled),
and a copy-paste schema sketch: [`docs/09-compute-and-data.md`](09-compute-and-data.md) §9.4–9.5.

- **`water`** — derived from the most severe live station reading in the
  province (e.g. a categorical situation level, or % of bank capacity).
  This should dominate the weighting: overflow is the rare, high-
  consequence event everything else exists to anticipate.
- **`rain`** — the worst 24-hour rainfall accumulation among the province's
  gauges. This is a **leading** indicator — it shows up hours before water
  levels respond — so it deserves real weight even though water level
  dominates.
- **`forecast`** — near-term (24–48h) precipitation forecast for the
  province. Useful as a bias-corrector, but too coarse at typical global
  forecast-model resolution to drive a ranking on its own — keep its
  weight modest.
- **`soil_wetness`** and **`rise_rate`** — see §4.3 and §4.4 below. Both are
  optional refinements you can add once the three core factors work.

Pick weights that are **defensible in a sentence**, not weights fitted by a
model you can't explain in the room. A sensible starting allocation gives
water level roughly 40–50% of the total, rain 25–30%, forecast 15–20%, and
the remaining 10–20% split between the refinements. Publish the exact
formula and the exact weights **in the product itself** — a hidden formula
that a provincial analyst can't audit is worse than no formula at all.

**TH.** ประโยคที่สำคัญที่สุดในเอกสารทั้งหมดนี้คือ: **คะแนนเฝ้าระวังที่คำนวณ
จากข้อมูลสดเป็นเพียงตัวชี้วัดเชิงประเมิน ไม่ใช่การพยากรณ์น้ำท่วม และ UI ของ
คุณต้องบอกแบบนี้ทุกหน้าจอที่คะแนนปรากฏ** เมื่อยืนยันแบบนี้แล้ว นี่คือรูปแบบ
สูตรที่ใช้งานได้จริง:

```
คะแนน = น้ำหนัก1·ระดับน้ำ + น้ำหนัก2·ฝน + น้ำหนัก3·พยากรณ์ + น้ำหนัก4·ความชุ่มดิน + น้ำหนัก5·อัตราเพิ่มระดับ
```

- **ระดับน้ำ** — มาจากค่าที่วิกฤตที่สุดของสถานีในจังหวัด (เช่น ระดับ
  สถานการณ์เชิงหมวดหมู่ หรือ % ความจุตลิ่ง) ควรได้น้ำหนักมากที่สุด เพราะการ
  ล้นตลิ่งคือเหตุการณ์ที่หายากแต่ผลกระทบรุนแรงที่สุดที่ทุกอย่างมีไว้เพื่อคาดการณ์
- **ฝน** — ฝนสะสม 24 ชม. ที่มากที่สุดในบรรดาสถานีของจังหวัด เป็นสัญญาณ
  **นำ** — มาก่อนระดับน้ำตอบสนองหลายชั่วโมง — จึงควรได้น้ำหนักจริง แม้ระดับน้ำ
  จะเด่นกว่า
- **พยากรณ์** — พยากรณ์ฝนระยะสั้น (24–48 ชม.) ของจังหวัด มีประโยชน์เป็นตัวแก้
  อคติ แต่หยาบเกินไปที่ความละเอียดของโมเดลพยากรณ์ระดับโลกทั่วไปจะขับเคลื่อน
  การจัดอันดับได้ด้วยตัวเอง — ให้น้ำหนักพอประมาณ
- **ความชุ่มดิน** และ **อัตราเพิ่มระดับ** — ดูหัวข้อ 4.3 และ 4.4 ด้านล่าง
  ทั้งสองเป็นส่วนขยายที่เพิ่มได้ทีหลัง เมื่อสามปัจจัยหลักทำงานแล้ว

เลือกน้ำหนักที่ **อธิบายได้ในหนึ่งประโยค** ไม่ใช่น้ำหนักที่ fit จากโมเดลที่
อธิบายในห้องประชุมไม่ได้ การจัดสรรเริ่มต้นที่สมเหตุสมผลคือระดับน้ำ 40–50%
ฝน 25–30% พยากรณ์ 15–20% และที่เหลือ 10–20% แบ่งให้ส่วนขยาย เผยแพร่สูตรและ
น้ำหนักที่แน่นอน **ในตัวผลิตภัณฑ์เอง** — สูตรที่ซ่อนไว้ซึ่งนักวิเคราะห์จังหวัด
ตรวจสอบไม่ได้แย่กว่าไม่มีสูตรเลย

## 4.2 Connected waterways: why upstream matters more than rain / เส้นทางน้ำเชื่อมโยง

**EN.** A river network is a **directed graph**: water always flows
downstream, and tributaries join at confluences. This single fact is the
basis of every lead-time flood warning ever issued, because **discharge
measured upstream today becomes a flood downstream in a predictable number
of days.**

Model this as a small graph: pick the handful of river reaches that matter
for your region (major confluences, the reach just below a large dam, the
reach through your largest city), give each one a **downstream link** and a
**transit lag** (the number of days a rise at this reach takes to show up
at the next one downstream), and pull discharge data from GloFAS (§3.2) for
each. The flagship worked example for Thailand is the Chao Phraya: the Ping
and Nan rivers join at Nakhon Sawan, the combined flow travels through Chai
Nat and Ayutthaya, and reaches Bangkok roughly **five days** after the
headwaters under normal flow conditions (this stretches to weeks during a
truly saturated, overflowing event — flood-wave speed is not constant, it
depends on how full the channel already is).

The physics in one sentence: a flood wave travels at the **kinematic wave
celerity**, `c ≈ (5/3)·V` (roughly five-thirds the mean flow velocity for a
wide channel) — faster than the water itself moves — which is exactly why
a flood crest can outrun floating debris. This is Lighthill & Whitham's
1955 kinematic-wave theory, and it underlies the industry-standard
Muskingum-Cunge routing method used in essentially every operational
hydrology model worldwide.

One engineering gotcha worth inheriting: **global river-discharge models
are typically gridded at multi-kilometre resolution.** A naive coordinate
lookup for "the river at city X" can land on a dry tributary a few
kilometres from the actual main channel. The fix is cheap: scan a small
grid of nearby coordinates and keep whichever cell reports the highest
discharge — that's almost always the true channel.

**TH.** เครือข่ายแม่น้ำเป็น **กราฟมีทิศทาง** — น้ำไหลลงปลายน้ำเสมอ ลำน้ำสาขา
มารวมกันที่จุดบรรจบ ข้อเท็จจริงเดียวนี้คือฐานของการเตือนภัยน้ำท่วมล่วงหน้าทุก
ครั้งที่เคยออก เพราะ **อัตราการไหลที่วัดได้ที่ต้นน้ำวันนี้จะกลายเป็นน้ำท่วมที่
ปลายน้ำในจำนวนวันที่คาดการณ์ได้**

สร้างเป็นกราฟเล็ก ๆ: เลือกจุดในลำน้ำไม่กี่จุดที่สำคัญกับพื้นที่ของคุณ (จุด
บรรจบหลัก จุดท้ายเขื่อนใหญ่ จุดที่ผ่านเมืองใหญ่ที่สุด) ให้แต่ละจุดมี
**ปลายน้ำที่เชื่อมไป** และ **เวลาเดินทาง** (จำนวนวันที่การเพิ่มขึ้นที่จุดนี้
ใช้เวลากว่าจะไปถึงจุดถัดไปทางปลายน้ำ) แล้วดึงข้อมูลอัตราการไหลจาก GloFAS
(หัวข้อ 3.2) ของแต่ละจุด ตัวอย่างหลักของไทยคือเจ้าพระยา — ปิงกับน่านมาบรรจบ
ที่นครสวรรค์ กระแสรวมไหลผ่านชัยนาทและอยุธยา ถึงกรุงเทพฯ ประมาณ **5 วัน** หลัง
ต้นน้ำในสภาวะไหลปกติ (ยืดยาวเป็นหลายสัปดาห์ในเหตุการณ์ที่อิ่มตัวและล้นตลิ่ง
จริง — ความเร็วคลื่นน้ำท่วมไม่คงที่ ขึ้นกับว่าลำน้ำเต็มอยู่แค่ไหนแล้ว)

ฟิสิกส์ในหนึ่งประโยค: คลื่นน้ำท่วมเดินทางด้วย **ความเร็วคลื่นจลนศาสตร์**
`c ≈ (5/3)·V` (ประมาณห้าในสามของความเร็วกระแสเฉลี่ยสำหรับลำน้ำกว้าง) — เร็ว
กว่าตัวน้ำเองเคลื่อนที่ — ซึ่งเป็นเหตุผลที่ยอดคลื่นน้ำท่วมแซงเศษวัสดุลอยได้
นี่คือทฤษฎีคลื่นจลนศาสตร์ของ Lighthill & Whitham ปี 1955 และเป็นฐานของวิธี
Muskingum-Cunge ที่เป็นมาตรฐานอุตสาหกรรมในแบบจำลองอุทกวิทยาปฏิบัติการเกือบ
ทุกระบบทั่วโลก

ข้อควรระวังทางวิศวกรรมที่คุ้มค่าจะรับไว้: **แบบจำลองอัตราการไหลแม่น้ำระดับ
โลกมักมีความละเอียดกริดระดับหลายกิโลเมตร** การค้นหาพิกัดแบบไร้เดียงสาสำหรับ
"แม่น้ำที่เมือง X" อาจตกลงบนสาขาแห้งที่ห่างจากลำน้ำหลักจริงไม่กี่กิโลเมตร
วิธีแก้ไขราคาถูก: สแกนกริดพิกัดใกล้เคียงเล็ก ๆ แล้วเก็บเซลล์ที่รายงานอัตรา
การไหลสูงสุด — นั่นเกือบทุกครั้งคือลำน้ำหลักจริง

## 4.3 Soil memory: the Antecedent Precipitation Index / ความจำของดิน

**EN.** Two provinces with identical rainfall today are **not** equally
dangerous if one has been soaking for a week. Dry soil has high
infiltration capacity — it absorbs a lot before runoff begins. Once
sustained rain saturates the soil, infiltration capacity collapses toward
zero (Horton, 1933) and nearly all further rain becomes surface runoff
straight into the channel — this is called **saturation-excess runoff**,
and it can turn identical rainfall into **three to four times** the flood
volume compared to a dry-soil scenario.

The classic, cheap way to track this without a soil-moisture satellite
product is the **Antecedent Precipitation Index (API)**, from Kohler &
Linsley's 1951 U.S. Weather Bureau research:

```
API_t = k · API_(t-1) + P_t
```

where `P_t` is today's rainfall and `k` is a daily decay coefficient
(typically 0.84–0.98 depending on basin drainage characteristics — a value
around 0.92 is a reasonable default, giving roughly a two-week meaningful
memory window). You can compute this entirely from **your own stored rain
history** — no external soil-moisture data source required. Use the
worst-gauge-per-region-per-day as a conservative proxy for catchment input,
since rainfall is spatially uneven and any single gauge is only a sample.

**TH.** สองจังหวัดที่ฝนเท่ากันวันนี้ **ไม่ได้** เสี่ยงเท่ากัน ถ้าจังหวัดหนึ่ง
โดนฝนต่อเนื่องมาทั้งอาทิตย์ ดินแห้งมีความสามารถซึมน้ำสูง — ดูดซับได้มากก่อน
จะเริ่มมีน้ำท่า เมื่อฝนต่อเนื่องทำให้ดินอิ่มตัว ความสามารถซึมน้ำยุบตัวลงเกือบ
เป็นศูนย์ (Horton, 1933) และฝนที่ตกต่อจากนั้นเกือบทั้งหมดกลายเป็นน้ำท่าไหลลง
ลำน้ำทันที เรียกว่า **น้ำท่าจาก saturation-excess** ซึ่งเปลี่ยนฝนปริมาณเท่า
เดิมให้กลายเป็นปริมาณน้ำท่วม **สามถึงสี่เท่า** เทียบกับสถานการณ์ดินแห้ง

วิธีคลาสสิกและราคาถูกในการติดตามเรื่องนี้โดยไม่ต้องพึ่งผลิตภัณฑ์ความชื้นดิน
จากดาวเทียมคือ **ดัชนีฝนสะสมถ่วงเวลา (API)** จากงานวิจัยของ Kohler &
Linsley ปี 1951 ของ U.S. Weather Bureau:

```
API_t = k · API_(t-1) + P_t
```

โดย `P_t` คือฝนวันนี้ และ `k` คือค่าสัมประสิทธิ์การลดค่ารายวัน (โดยทั่วไป
0.84–0.98 ขึ้นกับลักษณะการระบายน้ำของลุ่มน้ำ — ค่าประมาณ 0.92 เป็นค่าเริ่มต้น
ที่สมเหตุสมผล ให้ความจำที่มีนัยสำคัญประมาณสองสัปดาห์) คำนวณได้ทั้งหมดจาก
**ประวัติฝนที่ระบบเก็บเอง** — ไม่ต้องพึ่งแหล่งข้อมูลความชื้นดินภายนอก ใช้
สถานีที่ฝนมากสุดต่อพื้นที่ต่อวันเป็นตัวแทนอนุรักษ์นิยมของฝนที่เข้าลุ่มน้ำ
เพราะฝนกระจายไม่สม่ำเสมอในเชิงพื้นที่ และสถานีเดียวเป็นแค่ตัวอย่างหนึ่งจุด

## 4.4 Rate of rise: the leading indicator that beats absolute level / อัตราการเพิ่มระดับ

**EN.** A water level rising rapidly is a different emergency from the same
level holding steady. Track the change in water level over a short rolling
window (e.g. the last six hours) per station; a large positive rate is a
leading signal that precedes an absolute-level threshold crossing, and
gives your users earlier warning than a static level check alone.

**TH.** ระดับน้ำที่เพิ่มขึ้นเร็วเป็นภาวะฉุกเฉินที่ต่างจากระดับเดียวกันที่นิ่ง
ติดตามการเปลี่ยนแปลงของระดับน้ำในช่วงเวลาสั้น ๆ ที่เลื่อนไปเรื่อย ๆ (เช่น
6 ชั่วโมงที่ผ่านมา) ต่อสถานี อัตราเพิ่มที่สูงเป็นสัญญาณนำที่มาก่อนการข้าม
เกณฑ์ระดับสัมบูรณ์ และให้ผู้ใช้เตือนภัยได้เร็วกว่าการเช็คระดับนิ่ง ๆ อย่างเดียว

## 4.5 ENSO: a seasonal prior, never a station-level input / ENSO: ปัจจัยก่อนเหตุตามฤดูกาล

**EN.** The Oceanic Niño Index (a running mean of Pacific sea-surface
temperature anomalies, published freely by NOAA) classifies the ocean into
La Niña / neutral / El Niño phases. La Niña years correlate with higher
wet-season rainfall odds in Thailand (most clearly at an annual, national
scale) — Thailand's costliest flood on record was a La Niña year. **This is
a seasonal prior, useful as context for a briefing, and it should never be
folded into an individual station's live risk score.** Surface it
separately, label it clearly as seasonal context, and let the human
combine it with the live picture.

**TH.** ดัชนี Oceanic Niño Index (ค่าเฉลี่ยเคลื่อนที่ของความผิดปกติอุณหภูมิ
ผิวน้ำทะเลแปซิฟิก เผยแพร่ฟรีโดย NOAA) จำแนกมหาสมุทรเป็นเฟสลานีญา / กลาง /
เอลนีโญ ปีลานีญาสัมพันธ์กับโอกาสฝนมากในฤดูฝนของไทยสูงขึ้น (ชัดที่สุดใน
ระดับปีทั้งประเทศ) — น้ำท่วมที่สร้างความเสียหายสูงสุดในประวัติศาสตร์ไทยเกิด
ในปีลานีญา **นี่คือปัจจัยก่อนเหตุตามฤดูกาล มีประโยชน์เป็นบริบทสำหรับการ
บรีฟ และไม่ควรใส่รวมในคะแนนความเสี่ยงสดของสถานีใดสถานีหนึ่งเด็ดขาด** แสดง
แยกต่างหาก ระบุชัดเจนว่าเป็นบริบทตามฤดูกาล แล้วให้มนุษย์ผสมกับภาพสดเอง

## References / อ้างอิง

- Kohler, M. A. & Linsley, R. K. (1951). *Predicting the runoff from storm
  rainfall.* U.S. Weather Bureau Research Paper 34.
- Horton, R. E. (1933). *The rôle of infiltration in the hydrologic cycle.*
  Transactions, American Geophysical Union, 14, 446–460.
- Lighthill, M. J. & Whitham, G. B. (1955). *On kinematic waves I: Flood
  movement in long rivers.* Proceedings of the Royal Society A, 229,
  281–316.
- Chow, V. T., Maidment, D. R. & Mays, L. W. (1988). *Applied Hydrology.*
  McGraw-Hill. (Muskingum-Cunge routing.)
- NOAA Climate Prediction Center — Oceanic Niño Index:
  https://www.cpc.ncep.noaa.gov/data/indices/oni.ascii.txt
- Copernicus GloFAS via the Open-Meteo Flood API:
  https://open-meteo.com/en/docs/flood-api

---

[← Data Sources](03-data-sources.md) · [Next: Design Language →](05-design-language.md)
