# Deep dive 3: Hydrological models and the core equations
## เจาะลึก 3: แบบจำลองอุทกวิทยาและสมการหลัก

[← Previous](02-data-sources-deep-dive.md) · [Deep-dive index](README.md) · [Next →](04-machine-learning.md)

---

## Five model families, one comparison table / ห้าตระกูลแบบจำลอง หนึ่งตารางเปรียบเทียบ

**EN.** Operational flood forecasting in river basins like the Chao Phraya
draws on a handful of established model families. You don't need to
implement all of them — most teams starting out should reach for the
lightweight equations in the next section — but knowing the landscape
helps you talk to hydrologists and government partners in their own
vocabulary.

| Model | Spatial approach | Core physics | Typical time step | Notes |
|---|---|---|---|---|
| **RRI (Rainfall-Runoff-Inundation)** | 2D surface grid + 1D river network | Diffusive-wave shallow-water equations + Green-Ampt infiltration | Minutes | Free download; explicitly validated against the Chao Phraya basin through an international development-cooperation project; couples runoff generation and inundation mapping in one model |
| **HEC-HMS + HEC-RAS** | Sub-basin runoff → 1D/2D channel hydraulics | SCS Curve Number or continuous soil-moisture accounting → Saint-Venant equations | Hourly | A US Army Corps of Engineers standard, free, worldwide; commonly reported Nash-Sutcliffe Efficiency in the 0.7–0.9 range when calibrated for major Thai flood years |
| **A conceptual rainfall-runoff model + 1D hydrodynamic engine** (the pairing widely used by Thai hydro-agencies) | Conceptual 4-layer bucket model → full dynamic-wave river routing | Bucket-model evapotranspiration/storage physics → Saint-Venant | Runs 1–2×/day operationally | Proprietary commercial software; still worth understanding the *pattern* (conceptual rainfall-runoff feeding a full hydrodynamic river model) even if you substitute an open alternative |
| **SWAT (Soil and Water Assessment Tool)** | Sub-basin → Hydrologic Response Units | SCS-CN or Green-Ampt + lateral/groundwater flow | Daily | USDA-developed, free; suited to long-term water-balance and land-use-change studies, not real-time forecasting |
| **Wflow (Deltares)** | Fully distributed grid | Simple-bucket vertical model + kinematic-wave routing | Hourly | Fully open-source (permissive license), modern, designed for automated setup from global datasets — a strong candidate if your team wants a physically-based model without a commercial license |

**TH.** การพยากรณ์น้ำท่วมเชิงปฏิบัติการในลุ่มน้ำแบบเจ้าพระยาอิงกับแบบจำลอง
ไม่กี่ตระกูลที่เป็นที่ยอมรับ ไม่จำเป็นต้องสร้างทั้งหมด — ทีมส่วนใหญ่ที่เริ่ม
ต้นควรใช้สมการน้ำหนักเบาในหัวข้อถัดไป — แต่การรู้จักภูมิทัศน์นี้ช่วยให้คุย
กับนักอุทกวิทยาและพันธมิตรภาครัฐด้วยคำศัพท์เดียวกันได้

(ดูตารางด้านบน — ตระกูลแบบจำลอง แนวทางเชิงพื้นที่ ฟิสิกส์หลัก ช่วงเวลาทั่วไป
และหมายเหตุ)

## The core equations, implementable directly / สมการหลักที่นำไปใช้งานได้ตรง ๆ

### Rainfall → runoff: the SCS Curve Number method

**EN.** The most widely implemented rainfall-runoff equation, because its
data requirements are minimal:

```
Pe = (P − 0.2S)² / (P + 0.8S)          for P > 0.2S, else Pe = 0
S = 25400/CN − 254                      (S in mm)
```

where `P` is rainfall depth (mm), `Pe` is the resulting runoff depth (mm),
and `CN` (Curve Number, 30–100) encodes land cover and soil type — typical
values run roughly 55–75 for forested mountainous terrain, 70–85 for
general agriculture, 80–95 for urban/built-up areas, and 85–95 for paddy
fields. `CN` must be adjusted for antecedent moisture condition (wetter
antecedent conditions raise the effective CN, meaning more of the same
rainfall becomes runoff — see the soil-wetness discussion in the main
blueprint's [science document](../04-the-science.md)).

**TH.** สมการฝน-น้ำท่าที่ใช้งานแพร่หลายที่สุด เพราะต้องการข้อมูลน้อยที่สุด:

```
Pe = (P − 0.2S)² / (P + 0.8S)          เมื่อ P > 0.2S ไม่งั้น Pe = 0
S = 25400/CN − 254                      (S เป็น มม.)
```

โดย `P` คือความลึกฝน (มม.) `Pe` คือความลึกน้ำท่าที่ได้ (มม.) และ `CN`
(Curve Number, 30–100) เข้ารหัสประเภทการใช้ที่ดินและชนิดดิน — ค่าทั่วไปอยู่
ราว 55–75 สำหรับพื้นที่ป่าเขา 70–85 สำหรับเกษตรทั่วไป 80–95 สำหรับเมือง/
สิ่งปลูกสร้าง และ 85–95 สำหรับนาข้าว ต้องปรับ `CN` ตามสภาพความชื้นก่อนหน้า
(สภาพชื้นกว่าทำให้ CN ที่ใช้จริงสูงขึ้น หมายถึงฝนปริมาณเดียวกันกลายเป็นน้ำท่า
มากขึ้น — ดูการอภิปรายความชุ่มดินใน[เอกสารวิทยาศาสตร์](../04-the-science.md)
ของพิมพ์เขียวหลัก)

### Channel flow: Manning's equation

**EN.**

```
Q = (1/n) · A · R^(2/3) · S^(1/2)
```

where `Q` is discharge (m³/s), `n` is Manning's roughness coefficient,
`A` is cross-sectional flow area (m²), `R` is the hydraulic radius (m,
area divided by wetted perimeter), and `S` is the channel slope. Field
studies on the Chao Phraya have back-calculated station-specific `n`
values in the range **0.035 (upstream, cleaner channel) to 0.045
(downstream, denser aquatic vegetation)** — worth using as a starting
range for a Thai lowland river if you don't have local calibration data
yet, and worth noting that `n` is not static: it rises during the wet
season as aquatic vegetation (water hyacinth is a commonly cited culprit)
thickens.

**TH.**

```
Q = (1/n) · A · R^(2/3) · S^(1/2)
```

โดย `Q` คืออัตราการไหล (ลบ.ม./วิ) `n` คือสัมประสิทธิ์ความขรุขระของ Manning
`A` คือพื้นที่หน้าตัดการไหล (ตร.ม.) `R` คือรัศมีชลศาสตร์ (ม. พื้นที่หารเส้น
รอบวงเปียก) และ `S` คือความลาดชันลำน้ำ การศึกษาภาคสนามบนเจ้าพระยาคำนวณย้อน
กลับค่า `n` เฉพาะสถานีได้ในช่วง **0.035 (ต้นน้ำ ลำน้ำสะอาดกว่า) ถึง 0.045
(ปลายน้ำ พืชน้ำหนาแน่นกว่า)** — คุ้มค่าใช้เป็นช่วงเริ่มต้นสำหรับแม่น้ำที่ราบ
ลุ่มไทยถ้ายังไม่มีข้อมูลปรับเทียบท้องถิ่น และควรสังเกตว่า `n` ไม่คงที่: มันสูง
ขึ้นในฤดูฝนเมื่อพืชน้ำ (ผักตบชวาเป็นตัวการที่มักถูกอ้างถึง) หนาแน่นขึ้น

### Flood routing: Muskingum-Cunge

**EN.** The standard method for propagating a hydrograph downstream
through a channel reach, using parameters derived from physical channel
geometry rather than requiring calibration against observed data:

```
O(t+1) = C1·I(t+1) + C2·I(t) + C3·O(t)          [C1 + C2 + C3 = 1]

C1 = (0.5Δt − KX) / (K − KX + 0.5Δt)
C2 = (0.5Δt + KX) / (K − KX + 0.5Δt)
C3 = (K − KX − 0.5Δt) / (K − KX + 0.5Δt)

K = Δx / Ck                    (travel time through the reach)
Ck ≈ (5/3)·V                   (kinematic wave celerity, wide channel)
```

where `I`/`O` are inflow/outflow, `Δx` is reach length, `V` is mean flow
velocity, and `X` (0–0.5) is a weighting factor controlling how much the
flood peak attenuates versus simply translates downstream (`X = 0.5` = pure
translation, no attenuation; `X → 0` = maximum attenuation, reservoir-
like). For the Chao Phraya, reach travel times on the order of **2–12
hours** per modeled segment are typical, with `X` in the range **0.3–0.5**.

**TH.** วิธีมาตรฐานสำหรับส่งผ่านไฮโดรกราฟลงปลายน้ำผ่านช่วงลำน้ำ โดยใช้
พารามิเตอร์ที่ได้จากรูปทรงลำน้ำทางกายภาพ แทนที่จะต้องปรับเทียบกับข้อมูลที่
สังเกตได้:

```
O(t+1) = C1·I(t+1) + C2·I(t) + C3·O(t)          [C1 + C2 + C3 = 1]

K = Δx / Ck                    (เวลาเดินทางผ่านช่วงลำน้ำ)
Ck ≈ (5/3)·V                   (ความเร็วคลื่นจลนศาสตร์ ลำน้ำกว้าง)
```

โดย `I`/`O` คือน้ำเข้า/น้ำออก `Δx` คือความยาวช่วงลำน้ำ `V` คือความเร็วการ
ไหลเฉลี่ย และ `X` (0–0.5) คือปัจจัยถ่วงน้ำหนักที่ควบคุมว่ายอดน้ำท่วมลดทอนมาก
แค่ไหนเทียบกับแค่เคลื่อนที่ลงปลายน้ำเฉย ๆ (`X = 0.5` = เคลื่อนที่ล้วน ไม่ลด
ทอน; `X → 0` = ลดทอนสูงสุด แบบอ่างเก็บน้ำ) สำหรับเจ้าพระยา เวลาเดินทางต่อ
ช่วงแบบจำลองโดยทั่วไปอยู่ที่ **2–12 ชั่วโมง** โดย `X` อยู่ในช่วง **0.3–0.5**

### Flood-frequency analysis: return periods

**EN.** For infrastructure design and threshold-setting, two statistical
distributions dominate practice: **Gumbel (Extreme Value Type I)** and
**Log-Pearson Type III**, both fitted to a station's annual-maximum
discharge series to estimate the discharge associated with a given return
period (e.g. the "100-year flood"). Thai design practice spans return
periods from about 2 years (agricultural protection) to 200 years
(critical infrastructure); the 2011 event is estimated at roughly a
100–200-year return period at some locations — a reminder that "design"
events do occur, and a changing climate means historical return-period
statistics should be revisited periodically rather than treated as fixed
forever.

**TH.** สำหรับการออกแบบโครงสร้างพื้นฐานและการตั้งเกณฑ์ การแจกแจงทางสถิติ
สองแบบครองการปฏิบัติงาน: **Gumbel (Extreme Value Type I)** และ
**Log-Pearson Type III** ทั้งคู่ fit กับอนุกรมอัตราการไหลสูงสุดรายปีของสถานี
เพื่อประมาณอัตราการไหลที่สัมพันธ์กับคาบการเกิดซ้ำที่กำหนด (เช่น "น้ำท่วมรอบ
100 ปี") แนวปฏิบัติการออกแบบของไทยครอบคลุมคาบการเกิดซ้ำตั้งแต่ราว 2 ปี
(ป้องกันพื้นที่เกษตร) ถึง 200 ปี (โครงสร้างพื้นฐานวิกฤต) เหตุการณ์ปี 2554
ประเมินว่าอยู่ที่คาบการเกิดซ้ำราว 100–200 ปีในบางพื้นที่ — เครื่องเตือนใจว่า
เหตุการณ์ระดับ "ออกแบบ" เกิดขึ้นจริงได้ และภูมิอากาศที่เปลี่ยนแปลงหมายความ
ว่าสถิติคาบการเกิดซ้ำในอดีตควรทบทวนเป็นระยะ ไม่ใช่ถือว่าคงที่ตลอดไป

## How to know if your model is any good / จะรู้ได้อย่างไรว่าแบบจำลองของคุณใช้ได้

**EN.** Three metrics dominate hydrological model evaluation:

- **Nash-Sutcliffe Efficiency (NSE)** — the classic goodness-of-fit
  measure; 1.0 is perfect, 0.0 means "no better than just using the
  historical mean as your forecast," negative means worse than that.
  A commonly cited threshold for "reliable for operational use" is
  **NSE > 0.75**.
- **Kling-Gupta Efficiency (KGE)** — decomposes performance into three
  independent, individually diagnosable components (correlation,
  variability ratio, bias ratio), which avoids some counter-intuitive
  rankings that NSE alone can produce. Values above roughly 0.75 are
  generally considered good.
- **R² alone is a trap** — it measures only linear association and is
  blind to systematic bias, so a model can score a near-perfect R² while
  consistently over- or under-predicting magnitude. Never report R²
  without pairing it with NSE/KGE and a bias metric.

**TH.** สามตัวชี้วัดครองการประเมินแบบจำลองอุทกวิทยา:

- **Nash-Sutcliffe Efficiency (NSE)** — ตัวชี้วัดความพอดีคลาสสิก 1.0 คือ
  สมบูรณ์แบบ 0.0 หมายถึง "ไม่ดีกว่าใช้ค่าเฉลี่ยประวัติศาสตร์เป็นพยากรณ์เฉย ๆ"
  ค่าติดลบหมายถึงแย่กว่านั้น เกณฑ์ที่มักอ้างถึงสำหรับ "เชื่อถือได้สำหรับใช้
  งานจริง" คือ **NSE > 0.75**
- **Kling-Gupta Efficiency (KGE)** — แยกประสิทธิภาพเป็นสามองค์ประกอบอิสระที่
  วินิจฉัยแยกกันได้ (สหสัมพันธ์ อัตราส่วนความแปรปรวน อัตราส่วนอคติ) ซึ่งช่วย
  หลีกเลี่ยงการจัดอันดับที่ขัดสามัญสำนึกบางแบบที่ NSE เดี่ยว ๆ อาจให้ได้ ค่า
  สูงกว่าราว 0.75 โดยทั่วไปถือว่าดี
- **R² เดี่ยว ๆ เป็นกับดัก** — วัดแค่ความสัมพันธ์เชิงเส้น มองไม่เห็นอคติเชิง
  ระบบ ดังนั้นแบบจำลองอาจได้ R² เกือบสมบูรณ์แบบทั้งที่พยากรณ์ขนาดสูงหรือต่ำ
  กว่าจริงอย่างสม่ำเสมอ อย่ารายงาน R² โดยไม่จับคู่กับ NSE/KGE และตัวชี้วัดอคติ

## References / อ้างอิง

- Kohler, M. A. & Linsley, R. K. (1951). *Predicting the runoff from storm
  rainfall.* U.S. Weather Bureau Research Paper 34.
- Chow, V. T., Maidment, D. R. & Mays, L. W. (1988). *Applied Hydrology.*
  McGraw-Hill.
- Nash, J. E. & Sutcliffe, J. V. (1970). *River flow forecasting through
  conceptual models part I.* Journal of Hydrology, 10(3), 282–290.
- Gupta, H. V., Kling, H., Yilmaz, K. K. & Martinez, G. F. (2009).
  *Decomposition of the mean squared error and NSE performance criteria.*
  Journal of Hydrology, 377(1–2), 80–91.

---

[← Previous](02-data-sources-deep-dive.md) · [Deep-dive index](README.md) · [Next →](04-machine-learning.md)
