# Deep dive 4: Machine learning for flood prediction
## เจาะลึก 4: แมชชีนเลิร์นนิงสำหรับพยากรณ์น้ำท่วม

[← Previous](03-hydrological-models.md) · [Deep-dive index](README.md) · [Next →](05-risk-scoring-frameworks.md)

---

## The finding that should shape your feature engineering / ข้อค้นพบที่ควรกำหนดรูปแบบ feature engineering ของคุณ

**EN.** Across multiple independent studies, feature-importance analysis
(SHAP values, the standard method for explaining what a model actually
weighs) converges on one dominant result: **upstream discharge accounts
for roughly 70%+ of a model's predictive power**, far outweighing local
rainfall. Removing the upstream-discharge feature from a well-performing
model has been shown to collapse performance dramatically (one study
reported NSE dropping from ~0.95 to ~0.62), while removing rainfall alone
cost only a few percentage points. The reason: an upstream gauge already
integrates the effect of every rainfall event, soil condition, and
land-use pattern across the entire catchment above it — a single number
carries an enormous amount of upstream information. **This is the strongest
possible scientific validation for building a connected-waterways /
upstream-gauge-cascade design** (see the main blueprint's
[science document](../04-the-science.md), §4.2) rather than relying on
local rainfall alone.

**TH.** จากการศึกษาอิสระหลายชิ้น การวิเคราะห์ความสำคัญของฟีเจอร์ (ค่า SHAP
วิธีมาตรฐานสำหรับอธิบายว่าแบบจำลองให้น้ำหนักอะไรจริง ๆ) ลงเอยที่ผลลัพธ์
เด่นชัดหนึ่งข้อ: **อัตราการไหลต้นน้ำคิดเป็นราว 70%+ ของพลังพยากรณ์ของแบบ
จำลอง** มากกว่าฝนท้องถิ่นอย่างมาก การตัดฟีเจอร์อัตราการไหลต้นน้ำออกจากแบบ
จำลองที่ทำงานดีถูกแสดงให้เห็นว่าทำให้ประสิทธิภาพร่วงลงอย่างมาก (การศึกษาหนึ่ง
รายงาน NSE ลดจาก ~0.95 เหลือ ~0.62) ขณะที่ตัดฝนอย่างเดียวเสียแค่ไม่กี่
เปอร์เซ็นต์ เหตุผล: มาตรวัดต้นน้ำได้รวมผลของทุกเหตุการณ์ฝน สภาพดิน และรูป
แบบการใช้ที่ดินทั่วทั้งลุ่มน้ำเหนือจุดนั้นไว้แล้ว — ตัวเลขเดียวบรรทุกข้อมูล
ต้นน้ำมหาศาล **นี่คือการยืนยันทางวิทยาศาสตร์ที่หนักแน่นที่สุดสำหรับการสร้าง
ดีไซน์เส้นทางน้ำเชื่อมโยง/กราฟมาตรวัดต้นน้ำ** (ดู[เอกสารวิทยาศาสตร์](../04-the-science.md)
ของพิมพ์เขียวหลัก หัวข้อ 4.2) แทนที่จะพึ่งฝนท้องถิ่นอย่างเดียว

## The architecture lineage / สายวิวัฒนาการของสถาปัตยกรรม

**EN.** Global deep-learning hydrology has converged on LSTM-family
recurrent architectures as the state of the art, evolving in a traceable
lineage:

- **Entity-Aware LSTM (EA-LSTM)** — the architectural template most
  subsequent global models build on. Its core idea: separate **static**
  catchment attributes (elevation, slope, land cover, soil, basin area)
  from **dynamic** meteorological time-series inputs, feeding statics
  exclusively into the input gate while dynamics drive the forget/output
  gates — letting the model learn how catchment character modulates its
  response to weather, matching hydrological intuition. Typical production
  configuration: a single LSTM layer, 256 hidden units, a full year (365
  days) of daily input sequence per prediction, moderate dropout. A known
  weakness: it tends to **underestimate flood peaks** because standard
  mean-squared-error training smooths extremes — worth correcting with a
  loss function that up-weights high-flow periods if peak accuracy matters
  most to your use case.
- **Google's open-sourced global flood-forecasting model** (Apache 2.0
  licensed, publicly available) — the architecture behind a widely-used
  global flood-forecasting product covering 80+ countries including
  Thailand, with reported skill approaching NSE ≈ 0.95 on benchmark gauges.
  It separately embeds **hindcast** (verified historical) and **forecast**
  (uncertain future) meteorological inputs before combining them, and
  outputs a genuinely **probabilistic** forecast (a full distribution, not
  just a point estimate) over roughly a 7-day horizon. Being open-source
  and Apache-licensed, it is directly fine-tunable on Thai basin data —
  reported fine-tuning cost is on the order of ten GPU-hours.
- **A config-driven Python training framework** built around these
  architectures makes new-basin training a matter of writing a
  configuration file rather than custom training code — worth adopting
  rather than building your own training loop from scratch.

**TH.** การเรียนรู้เชิงลึกด้านอุทกวิทยาระดับโลกลงเอยที่สถาปัตยกรรม LSTM แบบ
วนซ้ำเป็นเทคโนโลยีล้ำสมัย วิวัฒนาการที่ตามรอยได้:

- **Entity-Aware LSTM (EA-LSTM)** — แม่แบบสถาปัตยกรรมที่แบบจำลองโลกส่วนใหญ่
  ต่อยอดจาก แนวคิดหลัก: แยกคุณลักษณะลุ่มน้ำแบบ **คงที่** (ความสูง ความชัน
  การใช้ที่ดิน ดิน พื้นที่ลุ่มน้ำ) จากข้อมูลนำเข้าอนุกรมเวลาอุตุนิยมวิทยาแบบ
  **เปลี่ยนแปลง** ป้อนค่าคงที่เข้า input gate อย่างเดียว ขณะที่ค่าเปลี่ยนแปลง
  ขับเคลื่อน forget/output gate — ให้แบบจำลองเรียนรู้ว่าลักษณะลุ่มน้ำปรับการ
  ตอบสนองต่อสภาพอากาศอย่างไร ตรงกับสัญชาตญาณอุทกวิทยา การตั้งค่าการผลิตทั่วไป:
  ชั้น LSTM ชั้นเดียว 256 หน่วยซ่อน ลำดับข้อมูลนำเข้ารายวันเต็มปี (365 วัน)
  ต่อการพยากรณ์หนึ่งครั้ง dropout ปานกลาง จุดอ่อนที่รู้จัก: มักจะ **ประเมิน
  ยอดน้ำท่วมต่ำกว่าจริง** เพราะการฝึกด้วย mean-squared-error มาตรฐานทำให้
  ค่าสุดโต่งเรียบขึ้น — คุ้มค่าแก้ด้วยฟังก์ชันสูญเสียที่ให้น้ำหนักช่วงน้ำมาก
  มากขึ้น ถ้าความแม่นยำของยอดสำคัญที่สุดสำหรับงานของคุณ
- **แบบจำลองพยากรณ์น้ำท่วมโลกที่ Google เปิดเป็นโอเพนซอร์ส** (สัญญาอนุญาต
  Apache 2.0 เปิดเผยสาธารณะ) — สถาปัตยกรรมเบื้องหลังผลิตภัณฑ์พยากรณ์น้ำท่วม
  โลกที่ใช้กันแพร่หลาย ครอบคลุมกว่า 80 ประเทศรวมถึงไทย มีทักษะที่รายงานเข้า
  ใกล้ NSE ≈ 0.95 บนมาตรวัดเกณฑ์เทียบ มัน embed ข้อมูลนำเข้าอุตุนิยมวิทยาแบบ
  **hindcast** (ประวัติศาสตร์ที่ยืนยันแล้ว) และ **forecast** (อนาคตที่ไม่
  แน่นอน) แยกกันก่อนรวมเข้าด้วยกัน แล้วให้ผลพยากรณ์แบบ **ความน่าจะเป็น**
  อย่างแท้จริง (การแจกแจงเต็ม ไม่ใช่แค่ค่าจุดเดียว) ในขอบฟ้าประมาณ 7 วัน
  เพราะเป็นโอเพนซอร์สสัญญาอนุญาต Apache จึง fine-tune บนข้อมูลลุ่มน้ำไทยได้
  โดยตรง — ต้นทุน fine-tuning ที่รายงานอยู่ที่ราวสิบ GPU-ชั่วโมง
- **เฟรมเวิร์กฝึกแบบ Python ที่ขับเคลื่อนด้วยไฟล์ config** สร้างรอบสถาปัตยกรรม
  เหล่านี้ทำให้การฝึกลุ่มน้ำใหม่เป็นแค่การเขียนไฟล์ configuration แทนที่จะ
  เขียนโค้ดฝึกเอง — คุ้มค่านำมาใช้แทนการสร้าง training loop ของตัวเองจากศูนย์

## Thailand-validated model scores, as a benchmark table / คะแนนแบบจำลองที่ตรวจสอบกับไทยแล้ว เป็นตารางเกณฑ์เทียบ

**EN.** Published studies on Thai basins (especially Chao Phraya dam
inflow prediction) give a useful benchmark table for anyone calibrating
their own models:

| Model | Task | Reported score | Notes |
|---|---|---|---|
| XGBoost | Daily dam inflow (Bhumibol) | NSE ≈ 0.86, R² ≈ 0.885 | Fast, CPU-only inference; strongest for univariate, strongly-autoregressive tasks |
| LSTM | Same task, same dataset | NSE ≈ 0.82 | Gradient boosting edged it out here — a useful reminder that fancier isn't always better for a specific task shape |
| Random Forest | Reservoir water-volume prediction | R² ≈ 0.97 | Best of six compared techniques on this task — **but** severely negative NSE at 99th-percentile (extreme) flows, meaning it should never be your only model for peak/extreme prediction |
| Random Forest | Flood-susceptibility classification | AUC ≈ 0.98 | Strong for binary flood-prone/not classification with a modest feature set |
| CNN-LSTM hybrid | Discharge in small northern Thai basins | NSE > 0.80 | Validated using satellite (not ground-gauge) precipitation input with sub-hourly latency — proof that satellite rainfall alone is sufficient for real-time discharge forecasting where gauges are sparse |
| An operational ML + optimization system | Multi-dam 14-day inflow prediction, used during a real recent flood season | Deployed operationally | Combines ML inflow forecasting with constraint-programming release optimization — a good template for "prediction + recommended action," not prediction alone |

**TH.** งานศึกษาที่ตีพิมพ์เกี่ยวกับลุ่มน้ำไทย (โดยเฉพาะการพยากรณ์น้ำไหลเข้า
เขื่อนเจ้าพระยา) ให้ตารางเกณฑ์เทียบที่มีประโยชน์สำหรับใครที่ปรับเทียบแบบ
จำลองของตัวเอง (ดูตารางด้านบน)

## Satellite-based inundation mapping / การทำแผนที่น้ำท่วมจากดาวเทียมด้วย ML

**EN.** For mapping *where* water actually is (as distinct from predicting
*how much* water is coming), the established deep-learning approach is a
U-Net-style segmentation network with a pretrained image-classification
backbone, fed three channels derived from radar-satellite imagery
(the two polarization channels plus their ratio). Reported benchmark
performance: Intersection-over-Union around 0.84, overall pixel accuracy
above 96%. A newer transformer-based segmentation architecture edges ahead
in some benchmarks but the U-Net approach remains the safer production
choice today for reliability, inference speed, and memory footprint.

**TH.** สำหรับทำแผนที่ *ตำแหน่งที่มีน้ำจริง* (ต่างจากการพยากรณ์ *ปริมาณน้ำ*
ที่กำลังมา) วิธีการเรียนรู้เชิงลึกที่เป็นที่ยอมรับคือเครือข่ายแบ่งส่วนภาพสไตล์
U-Net พร้อม backbone จำแนกภาพที่ฝึกไว้ล่วงหน้า ป้อนสามช่องสัญญาณที่ได้จาก
ภาพดาวเทียมเรดาร์ (สองช่อง polarization บวกอัตราส่วนของมัน) ประสิทธิภาพ
เกณฑ์เทียบที่รายงาน: Intersection-over-Union ประมาณ 0.84 ความแม่นยำระดับ
พิกเซลโดยรวมเกิน 96% สถาปัตยกรรมแบ่งส่วนภาพแบบ transformer รุ่นใหม่แซงหน้า
ในบางเกณฑ์เทียบ แต่วิธี U-Net ยังเป็นตัวเลือกการผลิตที่ปลอดภัยกว่าในวันนี้
สำหรับความน่าเชื่อถือ ความเร็วอนุมาน และการใช้หน่วยความจำ

## Feature engineering checklist / เช็คลิสต์ feature engineering

**EN.** Across studies, a consistent set of engineered features recurs as
high-value: rainfall at multiple aggregation windows (now / 24h / 48h /
7-day cumulative), a soil-wetness proxy (an Antecedent Precipitation Index
computed from your own rain history is a free substitute for satellite
soil-moisture products), the rate of change of water level over 6h and 24h
windows (a leading indicator ahead of absolute-threshold crossing), and
**cyclical encoding of time** (representing month/day/hour as sine/cosine
pairs so, e.g., December and January sit adjacent in feature space instead
of discontinuous integers). For basins without their own historical
training data, transfer learning from a cluster of hydrologically similar
"donor" basins consistently outperforms both purely local training and
training on every available basin indiscriminately — pick donors by
catchment-attribute similarity (area, slope, aridity, baseflow index), not
by convenience.

**TH.** จากหลายการศึกษา ชุดฟีเจอร์ที่ประดิษฐ์ขึ้นที่ปรากฏซ้ำ ๆ ว่ามีคุณค่าสูง
คือ: ฝนที่หน้าต่างสะสมหลายระดับ (ปัจจุบัน / 24 ชม. / 48 ชม. / สะสม 7 วัน)
ตัวแทนความชุ่มดิน (ดัชนีฝนสะสมถ่วงเวลาที่คำนวณจากประวัติฝนของตัวเองเป็นตัว
แทนฟรีสำหรับผลิตภัณฑ์ความชื้นดินจากดาวเทียม) อัตราการเปลี่ยนแปลงของระดับน้ำ
ในหน้าต่าง 6 ชม. และ 24 ชม. (สัญญาณนำก่อนการข้ามเกณฑ์สัมบูรณ์) และ
**การเข้ารหัสเวลาแบบวัฏจักร** (แทนเดือน/วัน/ชั่วโมงเป็นคู่ sine/cosine เพื่อ
ให้เช่น ธันวาคมกับมกราคมอยู่ติดกันในปริภูมิฟีเจอร์แทนที่จะเป็นจำนวนเต็มที่
ไม่ต่อเนื่อง) สำหรับลุ่มน้ำที่ไม่มีข้อมูลฝึกประวัติศาสตร์ของตัวเอง การถ่ายโอน
การเรียนรู้จากกลุ่มลุ่มน้ำ "ผู้บริจาค" ที่คล้ายกันทางอุทกวิทยาให้ผลดีกว่าทั้ง
การฝึกเฉพาะท้องถิ่นล้วน ๆ และการฝึกกับทุกลุ่มน้ำที่มีแบบไม่เลือกอย่างสม่ำเสมอ
— เลือกผู้บริจาคจากความคล้ายคลึงของคุณลักษณะลุ่มน้ำ (พื้นที่ ความชัน ความ
แห้งแล้ง ดัชนี baseflow) ไม่ใช่จากความสะดวก

---

[← Previous](03-hydrological-models.md) · [Deep-dive index](README.md) · [Next →](05-risk-scoring-frameworks.md)
