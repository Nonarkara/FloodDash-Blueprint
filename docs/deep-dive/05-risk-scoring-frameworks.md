# Deep dive 5: Risk-scoring frameworks
## เจาะลึก 5: กรอบการให้คะแนนความเสี่ยง

[← Previous](04-machine-learning.md) · [Deep-dive index](README.md) · [Next →](06-open-source-stack.md)

---

## Why simple additive scores fail / เหตุใดคะแนนบวกอย่างง่ายจึงล้มเหลว

**EN.** The most tempting first design for a risk score is a weighted sum:
`risk = w1·water + w2·rain + w3·forecast + ...`. It is intuitive, easy to
explain, and easy to implement — and it has one structural flaw that
matters a great deal for flood risk specifically: **a weighted sum can
never produce zero unless every single input is zero.** A station with
catastrophic water level but literally no rain in the last 24 hours (a
realistic scenario — heavy rain upstream, dry conditions locally) still
gets a nonzero contribution from every other term diluting the true
signal, and conversely a location with severe rain but structurally
low exposure (no nearby waterway, high ground) can accumulate a
misleadingly elevated score purely from the rain term. The professional
literature's answer is **multiplicative composition**: `Risk = Hazard ×
Vulnerability × (Exposure)`, so that if any single dominant factor is
truly zero, the overall risk collapses toward zero — matching the
real-world intuition that "no water, no flood" regardless of how bad
every other input looks.

**TH.** การออกแบบคะแนนความเสี่ยงแบบแรกที่ล่อใจที่สุดคือผลรวมถ่วงน้ำหนัก:
`risk = w1·น้ำ + w2·ฝน + w3·พยากรณ์ + ...` มันใช้งานง่าย อธิบายง่าย และ
ทำง่าย — และมันมีข้อบกพร่องเชิงโครงสร้างหนึ่งข้อที่สำคัญมากสำหรับความเสี่ยง
น้ำท่วมโดยเฉพาะ: **ผลรวมถ่วงน้ำหนักไม่มีทางเป็นศูนย์ได้ เว้นแต่ทุกอินพุตจะ
เป็นศูนย์หมด** สถานีที่มีระดับน้ำเลวร้ายแต่ไม่มีฝนเลยใน 24 ชั่วโมงที่ผ่านมา
(สถานการณ์ที่เป็นจริงได้ — ฝนหนักต้นน้ำ สภาพแห้งในท้องถิ่น) ยังได้รับส่วน
สนับสนุนที่ไม่เป็นศูนย์จากทุกเทอมอื่นเจือจางสัญญาณจริง และในทางกลับกัน
พื้นที่ที่มีฝนรุนแรงแต่มีความเสี่ยงเชิงโครงสร้างต่ำ (ไม่มีทางน้ำใกล้เคียง
พื้นที่สูง) อาจสะสมคะแนนที่สูงเกินจริงโดยหลอกล่อจากเทอมฝนเพียงอย่างเดียว
คำตอบจากวรรณกรรมวิชาชีพคือ **การประกอบแบบคูณ**: `ความเสี่ยง = อันตราย ×
ความเปราะบาง × (การเผชิญ)` เพื่อให้หากปัจจัยเด่นตัวใดตัวหนึ่งเป็นศูนย์จริง
ความเสี่ยงโดยรวมจะยุบลงสู่ศูนย์ — ตรงกับสัญชาตญาณโลกจริงที่ว่า "ไม่มีน้ำ
ไม่มีน้ำท่วม" ไม่ว่าอินพุตอื่นจะดูเลวร้ายแค่ไหน

## Four established multiplicative frameworks / สี่กรอบการคูณที่เป็นที่ยอมรับ

**EN.** FloodDash's own risk engine (see the main blueprint's
[science document](../04-the-science.md)) currently uses a documented,
honestly-labeled additive heuristic — deliberately simple for a v1, and
explicitly NOT presented as a validated multiplicative model. For anyone
building toward a more rigorous v2, four established frameworks are worth
studying, each with a different philosophy:

| Framework | Core formula | Philosophy | Best fit |
|---|---|---|---|
| **FRI** (Flood Risk Index, academic hydrology literature) | `FRI = FHI × FVI` (Flood Hazard Index × Flood Vulnerability Index) | Cleanly separates "how bad is the physical hazard" from "how exposed/fragile is what's in its path" | Basin-level planning where hazard and vulnerability data are both available at matching resolution |
| **INFORM Risk Index** (EU Joint Research Centre / UN, used for global humanitarian crisis anticipation) | `Risk = Hazard&Exposure × Vulnerability × Lack-of-Coping-Capacity` (geometric mean of three pillars, each built from weighted sub-indicators) | Explicitly bakes in *societal* capacity to cope — the same physical hazard is scored as higher risk where institutional response capacity is weaker | Cross-province or cross-country comparison where you want to fold in governance/capacity differences, not just physical hazard |
| **WorldRiskIndex** (UNU-EHS, annual global report) | `WRI = Exposure × Vulnerability`, where Vulnerability itself decomposes into Susceptibility × Lack-of-Coping-Capacities × Lack-of-Adaptive-Capacities | A deeper vulnerability decomposition than INFORM — distinguishes "coping today" from "adapting over the long run" | Longitudinal tracking of whether a region's *resilience* is improving over years, not just its acute risk |
| **FEMA National Risk Index** (USA, but methodologically transferable) | `Risk = Expected Annual Loss × Social Vulnerability × (1 / Community Resilience)` | Denominates risk in **expected annual monetary/human loss**, not an abstract 0–100 score — makes cost-benefit conversations with funders concrete | Justifying infrastructure investment or insurance/funding proposals where a stakeholder wants a dollar figure, not a color band |

**TH.** เครื่องมือให้คะแนนความเสี่ยงของ FloodDash เอง (ดู
[เอกสารวิทยาศาสตร์](../04-the-science.md) ของพิมพ์เขียวหลัก) ปัจจุบันใช้
heuristic แบบบวกที่บันทึกไว้และติดป้ายอย่างซื่อสัตย์ — ตั้งใจให้ง่ายสำหรับ
v1 และไม่ได้นำเสนอว่าเป็นแบบจำลองการคูณที่ผ่านการตรวจสอบแล้ว สำหรับใครที่
กำลังสร้างไปสู่ v2 ที่เข้มงวดกว่า มีสี่กรอบที่เป็นที่ยอมรับควรศึกษา แต่ละ
กรอบมีปรัชญาต่างกัน (ดูตารางด้านบน)

## Weight-determination methods / วิธีกำหนดน้ำหนัก

**EN.** Every framework above still needs weights for its sub-indicators
— and picking those weights by gut feeling is the most common way risk
indices lose credibility. Three established, defensible methods:

- **AHP (Analytic Hierarchy Process)** — domain experts perform pairwise
  comparisons ("is water level more or less important than rainfall, and
  by roughly how much, on a 1–9 scale?") for every pair of factors; the
  method then derives weights via the comparison matrix's principal
  eigenvector, **and includes a built-in consistency check** (a
  consistency ratio below ~0.1 is required) that flags when the experts'
  pairwise judgments were internally contradictory. This is the most
  widely used method precisely because the consistency check catches
  sloppy expert elicitation before it poisons the weights.
- **Entropy weighting** — a fully data-driven alternative requiring no
  expert panel at all: indicators whose values vary a lot across
  stations/provinces (high information entropy) are automatically
  weighted more heavily than indicators that barely vary (which carry
  little discriminating power). Cheap to compute, fully objective, but
  blind to domain knowledge — a rarely-varying indicator might still be
  critically important (e.g., dam-failure risk) and entropy weighting
  would underweight it purely because it's usually stable.
  **The strongest practical designs combine both**: AHP for the
  domain-knowledge prior, entropy weighting for the objective correction,
  averaged or combined by a documented rule.
- **Game-theory-combined weighting** — treats each weighting method's
  output as one "player's" proposed strategy and finds a combination
  that minimizes the deviation from all methods simultaneously (a
  Nash-equilibrium-style aggregation), used in recent (2020s) hydrology
  literature to combine AHP + entropy + one or two other methods into a
  single defensible weight set rather than picking one arbitrarily.

**TH.** ทุกกรอบข้างต้นยังต้องการน้ำหนักสำหรับตัวชี้วัดย่อย — และการเลือก
น้ำหนักด้วยความรู้สึกเป็นวิธีที่พบบ่อยที่สุดที่ทำให้ดัชนีความเสี่ยงเสีย
ความน่าเชื่อถือ สามวิธีที่เป็นที่ยอมรับและป้องกันได้:

- **AHP (กระบวนการลำดับชั้นเชิงวิเคราะห์)** — ผู้เชี่ยวชาญทำการเปรียบเทียบ
  เป็นคู่ ("ระดับน้ำสำคัญกว่าหรือน้อยกว่าปริมาณฝน และประมาณเท่าไหร่ ในสเกล
  1-9?") สำหรับทุกคู่ปัจจัย จากนั้นวิธีนี้จะได้น้ำหนักผ่าน eigenvector หลัก
  ของเมทริกซ์เปรียบเทียบ **และมีการตรวจสอบความสอดคล้องในตัว** (อัตราส่วน
  ความสอดคล้องต่ำกว่า ~0.1 ที่จำเป็น) ที่ตรวจจับเมื่อการตัดสินแบบคู่ของ
  ผู้เชี่ยวชาญขัดแย้งกันเอง นี่คือวิธีที่ใช้กันแพร่หลายที่สุดเพราะการตรวจ
  สอบความสอดคล้องจับการรวบรวมความเห็นผู้เชี่ยวชาญที่หละหลวมก่อนที่จะทำลาย
  น้ำหนัก
- **การถ่วงน้ำหนักแบบเอนโทรปี** — ทางเลือกที่ขับเคลื่อนด้วยข้อมูลล้วน ๆ
  ไม่ต้องมีคณะผู้เชี่ยวชาญเลย: ตัวชี้วัดที่ค่าแปรผันมากข้ามสถานี/จังหวัด
  (เอนโทรปีข้อมูลสูง) จะได้น้ำหนักมากกว่าโดยอัตโนมัติเทียบกับตัวชี้วัดที่
  แทบไม่แปรผัน (ซึ่งมีอำนาจแยกแยะน้อย) คำนวณถูก เป็นภววิสัยเต็มที่ แต่มอง
  ไม่เห็นความรู้เฉพาะทาง — ตัวชี้วัดที่แปรผันน้อยอาจยังสำคัญมาก (เช่น
  ความเสี่ยงเขื่อนแตก) และการถ่วงน้ำหนักแบบเอนโทรปีจะให้น้ำหนักต่ำเกินไป
  เพียงเพราะมันมักจะคงที่ **การออกแบบที่แข็งแกร่งที่สุดในทางปฏิบัติผสมทั้ง
  สอง**: AHP สำหรับ prior ความรู้เฉพาะทาง เอนโทรปีสำหรับการแก้ไขเชิงภววิสัย
  เฉลี่ยหรือรวมกันด้วยกฎที่บันทึกไว้
- **การถ่วงน้ำหนักรวมด้วยทฤษฎีเกม** — ปฏิบัติต่อผลลัพธ์ของแต่ละวิธีถ่วง
  น้ำหนักเป็นกลยุทธ์ที่เสนอโดย "ผู้เล่น" หนึ่งคน และหาการรวมกันที่ลดความ
  เบี่ยงเบนจากทุกวิธีพร้อมกัน (การรวมสไตล์จุดสมดุลแนช) ใช้ในวรรณกรรม
  อุทกวิทยาล่าสุด (ยุค 2020) เพื่อรวม AHP + เอนโทรปี + อีกหนึ่งหรือสองวิธี
  เข้าเป็นชุดน้ำหนักที่ป้องกันได้ชุดเดียวแทนที่จะเลือกวิธีเดียวตามใจ

## A 3-phase evolution path / เส้นทางวิวัฒนาการ 3 ระยะ

**EN.** For a team starting from scratch, jumping straight to a
game-theory-combined multiplicative framework is over-engineering. A more
realistic path:

- **Phase 1 (v1, what FloodDash itself ships today)**: a documented,
  transparently-labeled additive heuristic. The honesty of the label
  ("watch heuristic, not a forecast") matters more at this stage than the
  formula's sophistication — resist the temptation to oversell it.
- **Phase 2**: convert to multiplicative composition (`Hazard ×
  Vulnerability`) once you have at least a rough vulnerability layer
  (population density, structure counts, or even just historical
  flood-frequency-per-location as a proxy) to multiply against the hazard
  signal — this alone fixes the "can't reach zero" problem.
  Weight the hazard sub-factors with AHP against a small panel of
  hydrologists/RID staff if you can get access to any.
  **(single-source recommendation** — this specific phase ordering is
  this author's synthesis of common practice, not a codified standard;
  treat it as a reasonable default, not a rule.)
- **Phase 3**: layer in entropy-weighted objective correction once you
  have enough historical station data (a year or more) for entropy to be
  statistically meaningful, and consider a proper validated ML model
  (see [deep dive 4](04-machine-learning.md)) as a parallel, cross-checked
  second opinion rather than a wholesale replacement — operational flood
  systems worldwide tend to run a simple transparent heuristic *and* an ML
  model side by side precisely because operators trust the heuristic's
  explainability and the ML model's accuracy for different purposes.

**TH.** สำหรับทีมที่เริ่มจากศูนย์ การกระโดดตรงไปกรอบการคูณที่รวมด้วย
ทฤษฎีเกมเป็นการออกแบบเกินความจำเป็น เส้นทางที่สมจริงกว่า:

- **ระยะ 1 (v1 ที่ FloodDash เองใช้งานอยู่วันนี้)**: heuristic แบบบวกที่
  บันทึกไว้และติดป้ายอย่างโปร่งใส ความซื่อสัตย์ของป้ายกำกับ ("เกณฑ์เฝ้าระวัง
  ไม่ใช่การพยากรณ์") สำคัญกว่าความซับซ้อนของสูตรในขั้นตอนนี้ — ต้านทานการล่อ
  ใจที่จะขายเกินจริง
- **ระยะ 2**: แปลงเป็นการประกอบแบบคูณ (`อันตราย × ความเปราะบาง`) เมื่อคุณมี
  ชั้นความเปราะบางคร่าว ๆ อย่างน้อยหนึ่งชั้น (ความหนาแน่นประชากร จำนวนอาคาร
  หรือแม้แต่ความถี่น้ำท่วมย้อนหลังต่อพื้นที่เป็นตัวแทน) เพื่อคูณกับสัญญาณ
  อันตราย — แค่นี้ก็แก้ปัญหา "ไปถึงศูนย์ไม่ได้" แล้ว ถ่วงน้ำหนักปัจจัยย่อย
  อันตรายด้วย AHP กับคณะนักอุทกวิทยา/เจ้าหน้าที่ RID ขนาดเล็กถ้าเข้าถึงได้
  **(คำแนะนำแหล่งเดียว** — ลำดับระยะเฉพาะนี้เป็นการสังเคราะห์ของผู้เขียนจาก
  แนวปฏิบัติทั่วไป ไม่ใช่มาตรฐานที่บัญญัติไว้ ถือเป็นค่าเริ่มต้นที่สมเหตุสมผล
  ไม่ใช่กฎ)
- **ระยะ 3**: เพิ่มการแก้ไขเชิงภววิสัยด้วยการถ่วงน้ำหนักเอนโทรปีเมื่อคุณมี
  ข้อมูลสถานีย้อนหลังเพียงพอ (หนึ่งปีขึ้นไป) ให้เอนโทรปีมีนัยสำคัญทางสถิติ
  และพิจารณาแบบจำลอง ML ที่ผ่านการตรวจสอบแล้วอย่างเหมาะสม (ดู
  [เจาะลึก 4](04-machine-learning.md)) เป็นความเห็นที่สองแบบขนานที่ตรวจ
  สอบไขว้ ไม่ใช่การแทนที่ทั้งหมด — ระบบน้ำท่วมปฏิบัติการทั่วโลกมักรัน
  heuristic ที่โปร่งใสอย่างง่าย *และ* แบบจำลอง ML คู่ขนานกันเพราะผู้ปฏิบัติ
  งานเชื่อถือความอธิบายได้ของ heuristic และความแม่นยำของแบบจำลอง ML เพื่อ
  วัตถุประสงค์ที่ต่างกัน

## References / เอกสารอ้างอิง

- Saaty, T.L. (1980). *The Analytic Hierarchy Process.* McGraw-Hill.
- Birkmann, J. et al. (2013). *WorldRiskIndex: Concept and Results.*
  United Nations University Institute for Environment and Human Security.
- European Commission Joint Research Centre. *INFORM Risk Index
  Methodology.*
- FEMA (2021). *National Risk Index: Technical Documentation.*

---

[← Previous](04-machine-learning.md) · [Deep-dive index](README.md) · [Next →](06-open-source-stack.md)
