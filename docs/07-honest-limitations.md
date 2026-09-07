# 7. Honest limitations and the ethical framing / ข้อจำกัดที่ต้องพูดตรง ๆ และกรอบจริยธรรม

[← Roadmap](06-build-your-own-roadmap.md) · [Next: Lessons from production →](08-lessons-from-production.md)

---

## What a system built this way cannot do / สิ่งที่ระบบแบบนี้ทำไม่ได้

**EN.**

- **It is not a flood forecast model.** Every score, cascade, and trend
  arrow described in this blueprint is a **heuristic built from live
  observations** — it tells you what the data looks like right now and how
  it's trending, not what will physically happen. A real hydrological
  forecast requires calibrated physical models (rainfall-runoff, channel
  routing, floodplain inundation) that this blueprint deliberately does not
  attempt to replace.
- **It does not know soil conditions, drainage capacity, or hour-by-hour
  dam operations** beyond what the Antecedent Precipitation Index and
  public dam-release feeds already expose. A dam operator's real-time
  decision can change downstream flow in ways no public API reports in
  advance.
- **Global gridded data (river discharge, weather forecasts) has real
  resolution limits.** A model cell that's several kilometres wide can miss
  a local drainage bottleneck entirely, or straddle a levee. Local
  catchment response — especially in small, steep, urban catchments — can
  be faster and more severe than a coarse grid cell suggests.
- **A seasonal climate index (like ENSO) is a prior, not a prediction.**
  It shifts the odds over a season; it says nothing about next Tuesday.
- **News-feed cross-referencing is only as good as its keyword filter** and
  is never a substitute for verified local reporting.
- **A local-language-model chat feature is only as trustworthy as its
  grounding.** If you build one, it must answer exclusively from your
  database's real numbers — never let a model estimate or "helpfully" fill
  in a number it wasn't given.

**TH.**

- **นี่ไม่ใช่แบบจำลองพยากรณ์น้ำท่วม** คะแนน กราฟต้นน้ำ-ปลายน้ำ และลูกศร
  แนวโน้มทุกอย่างในพิมพ์เขียวนี้เป็น **ตัวชี้วัดเชิงประเมินที่สร้างจากการ
  สังเกตสด** — บอกว่าข้อมูลตอนนี้เป็นอย่างไรและแนวโน้มไปทางไหน ไม่ใช่สิ่งที่
  จะเกิดขึ้นจริงทางกายภาพ การพยากรณ์อุทกวิทยาจริงต้องใช้แบบจำลองทางกายภาพที่
  ปรับเทียบแล้ว (ฝน-น้ำท่า การเดินทางในลำน้ำ การท่วมพื้นที่ราบลุ่ม) ซึ่ง
  พิมพ์เขียวนี้ตั้งใจไม่พยายามแทนที่
- **ไม่รู้สภาพดินละเอียด ความจุระบบระบายน้ำ หรือการดำเนินงานเขื่อนรายชั่วโมง**
  เกินกว่าที่ดัชนีฝนสะสมถ่วงเวลาและฟีดการระบายเขื่อนสาธารณะเปิดเผยอยู่แล้ว
  การตัดสินใจแบบเรียลไทม์ของผู้ดูแลเขื่อนเปลี่ยนการไหลปลายน้ำได้ในแบบที่ไม่มี
  API สาธารณะรายงานล่วงหน้า
- **ข้อมูลกริดระดับโลก (อัตราการไหลแม่น้ำ พยากรณ์อากาศ) มีข้อจำกัดความ
  ละเอียดจริง** เซลล์แบบจำลองที่กว้างหลายกิโลเมตรอาจพลาดคอขวดการระบายน้ำ
  ท้องถิ่นไปเลย หรือคร่อมคันดิน การตอบสนองของลุ่มน้ำท้องถิ่น — โดยเฉพาะลุ่ม
  น้ำเล็ก ลาดชัน ในเมือง — อาจเร็วและรุนแรงกว่าที่เซลล์กริดหยาบบอกไว้
- **ดัชนีภูมิอากาศตามฤดูกาล (เช่น ENSO) เป็นปัจจัยก่อนเหตุ ไม่ใช่การพยากรณ์**
  มันเปลี่ยนโอกาสตลอดทั้งฤดู ไม่ได้บอกอะไรเกี่ยวกับวันอังคารหน้า
- **การอ้างอิงข้ามกับฟีดข่าวดีได้แค่ตัวกรองคำสำคัญ** และไม่มีวันแทนที่การ
  รายงานท้องถิ่นที่ตรวจสอบแล้วได้
- **ฟีเจอร์แชท local language model น่าเชื่อถือได้แค่การอ้างอิงของมัน**
  ถ้าคุณสร้างขึ้น มันต้องตอบจากตัวเลขจริงในฐานข้อมูลของคุณเท่านั้น — อย่าให้
  โมเดลประมาณหรือ "ช่วยเติม" ตัวเลขที่ไม่ได้ให้มันเด็ดขาด

## The ethical framing / กรอบจริยธรรม

**EN.** A tool like this sits close to public safety, which means two
disciplines matter more than usual:

1. **Never let the tool present itself as more authoritative than it is.**
   Every screen, every chat answer, every exported report should carry the
   same honest label: *heuristic indicator, not an official warning.*
   Official flood warnings come from your country's meteorological and
   disaster-management agencies — a community or provincial tool like this
   is built to help people **prioritise attention** and read public data
   faster, not to replace the institutions whose legal mandate is to issue
   warnings.
2. **Never fabricate a number.** It is tempting, when a data source is
   temporarily unavailable, to interpolate, estimate, or show a stale value
   as if it were current. Resist this completely. A blank space that says
   "no data since 14:32" is more honest, and ultimately more useful to a
   trained analyst, than a smooth line that hides a real gap. This
   principle should be non-negotiable in your team's engineering culture,
   not just a line in a document.

**TH.** เครื่องมือแบบนี้อยู่ใกล้ความปลอดภัยสาธารณะ ซึ่งแปลว่ามีวินัยสองข้อ
ที่สำคัญกว่าปกติ:

1. **อย่าให้เครื่องมือนำเสนอตัวเองว่ามีอำนาจมากกว่าที่เป็นจริง** ทุกหน้าจอ
   ทุกคำตอบแชท ทุกรายงานที่ส่งออก ควรมีป้ายกำกับที่ซื่อสัตย์เหมือนกัน:
   *ตัวชี้วัดเชิงประเมิน ไม่ใช่ประกาศเตือนภัยทางการ* ประกาศเตือนภัยน้ำท่วม
   ทางการมาจากหน่วยงานอุตุนิยมวิทยาและจัดการภัยพิบัติของประเทศคุณ — เครื่องมือ
   ระดับชุมชนหรือจังหวัดแบบนี้สร้างขึ้นเพื่อช่วยคน **จัดลำดับความสนใจ** และ
   อ่านข้อมูลสาธารณะได้เร็วขึ้น ไม่ใช่แทนที่หน่วยงานที่มีอำนาจตามกฎหมายในการ
   ออกประกาศเตือนภัย
2. **อย่าสร้างตัวเลขปลอมเด็ดขาด** เป็นเรื่องยั่วใจ เมื่อแหล่งข้อมูลใช้ไม่ได้
   ชั่วคราว ที่จะ interpolate ประมาณการ หรือแสดงค่าเก่าราวกับเป็นค่าปัจจุบัน
   ต้านทานสิ่งนี้อย่างสมบูรณ์ ช่องว่างที่บอกว่า "ไม่มีข้อมูลตั้งแต่ 14:32"
   ซื่อสัตย์กว่า และสุดท้ายมีประโยชน์กว่าสำหรับนักวิเคราะห์ที่ผ่านการฝึกฝน
   มากกว่าเส้นเรียบที่ซ่อนช่องว่างจริง หลักการนี้ควรต่อรองไม่ได้ในวัฒนธรรม
   วิศวกรรมของทีมคุณ ไม่ใช่แค่บรรทัดหนึ่งในเอกสาร

## The invitation, restated / คำเชิญ ย้ำอีกครั้ง

**EN.** If you build a version of this — better, worse, different, for a
different country entirely — I want to hear about it. Open an issue on
this repository, or reach out directly (see the README). The point of
publishing a blueprint instead of code was never secrecy for its own sake.
It was a bet that **enough capable people, given the real ideas and the
real data sources, will build real things.** Prove the bet right.

**TH.** ถ้าคุณสร้างเวอร์ชันของสิ่งนี้ — ดีกว่า แย่กว่า ต่างออกไป หรือสำหรับ
ประเทศอื่นไปเลย ผมอยากรู้ เปิด issue ใน repository นี้ หรือติดต่อโดยตรง
(ดูใน README) จุดประสงค์ของการเผยแพร่พิมพ์เขียวแทนโค้ดไม่เคยเป็นความลับเพื่อ
ความลับ แต่เป็นการเดิมพันว่า **คนที่มีความสามารถมากพอ เมื่อได้แนวคิดจริงและ
แหล่งข้อมูลจริง จะสร้างสิ่งจริงขึ้นมา** พิสูจน์ว่าการเดิมพันนี้ถูกต้อง

---

[← Roadmap](06-build-your-own-roadmap.md) · [Next: Lessons from production →](08-lessons-from-production.md)
