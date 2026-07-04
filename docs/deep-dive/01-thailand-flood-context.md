# Deep dive 1: Thailand's flood context, in forensic detail
## เจาะลึก 1: บริบทน้ำท่วมของไทยอย่างละเอียด

[← Deep-dive index](README.md) · [Next →](02-data-sources-deep-dive.md)

---

## The 2011 Great Flood, by the numbers / มหาอุทกภัย 2554 เป็นตัวเลข

**EN.** The 2011 Chao Phraya flood is the reference disaster for every flood
system built in Thailand since, so the exact numbers matter:

- **815 deaths, 13.6 million people affected, ~USD 46.5 billion in damage**
  (roughly 12.8% of GDP that year) — one of the costliest freshwater floods
  in modern global history, ranked by some post-disaster assessments as the
  4th-costliest insured event worldwide between 1970–2011.
- It was not one storm but a **compound sequence**: five tropical
  storms/depressions between July and October dumped 150–200% of normal
  rainfall across the basin, arriving on top of already-elevated reservoir
  levels.
- **Peak discharge at Nakhon Sawan (where the Ping and Nan rivers join)
  reached roughly 4,200 m³/s** — nearly double the channel's design
  capacity — and the flood wave took on the order of **six weeks** to
  travel from the northern apex of the basin down to Bangkok's fringe,
  because the lower Chao Phraya is extremely flat (a gradient on the order
  of 1.5 m per 100 km), so floodwater drains away very slowly once it
  arrives.
- A widely-cited post-event forensic finding: **optimized dam-release
  operations alone — no new construction — could plausibly have reduced
  peak discharge by roughly 400 m³/s.** In other words, a meaningful share
  of the damage was a decision-support and coordination failure, not
  purely a hydrological inevitability. This is the single strongest
  argument for building integration tools like this one.
- 65 of Thailand's 77 provinces were affected; seven major industrial
  estates in the Ayutthaya/Pathum Thani area flooded, disrupting global
  supply chains for hard-disk drives and automotive components for months.

**TH.** น้ำท่วมเจ้าพระยาปี 2554 เป็นภัยพิบัติอ้างอิงของทุกระบบน้ำท่วมที่สร้าง
ในไทยตั้งแต่นั้นมา ตัวเลขที่แน่นอนจึงสำคัญ:

- **เสียชีวิต 815 คน กระทบ 13.6 ล้านคน ความเสียหายราว 46,500 ล้านดอลลาร์
  สหรัฐ** (ประมาณ 12.8% ของ GDP ปีนั้น) — หนึ่งในน้ำท่วมน้ำจืดที่สร้างความ
  เสียหายสูงสุดในประวัติศาสตร์โลกยุคใหม่ บางการประเมินหลังภัยพิบัติจัดเป็น
  เหตุการณ์ที่มีการประกันภัยจ่ายสินไหมสูงเป็นอันดับ 4 ของโลกช่วงปี 1970–2011
- ไม่ใช่พายุลูกเดียว แต่เป็น **เหตุการณ์ผสมต่อเนื่อง**: พายุ/หย่อมความกดอากาศ
  ต่ำ 5 ลูกระหว่างกรกฎาคม-ตุลาคม ทำให้ฝนสะสมทั้งลุ่มน้ำสูงกว่าปกติ 150–200%
  มาซ้อนทับกับระดับน้ำในเขื่อนที่สูงอยู่แล้ว
- **อัตราการไหลสูงสุดที่นครสวรรค์ (จุดที่แม่น้ำปิงกับน่านมาบรรจบ) สูงถึง
  ราว 4,200 ลูกบาศก์เมตร/วินาที** — เกือบสองเท่าของความจุออกแบบของลำน้ำ —
  และคลื่นน้ำท่วมใช้เวลาประมาณ **หกสัปดาห์** กว่าจะเดินทางจากยอดลุ่มน้ำทาง
  เหนือลงมาถึงชานกรุงเทพฯ เพราะเจ้าพระยาตอนล่างราบเรียบมาก (ความลาดชัน
  ประมาณ 1.5 เมตรต่อ 100 กิโลเมตร) น้ำที่มาถึงจึงระบายออกช้ามาก
- ข้อค้นพบเชิงนิติวิทยาศาสตร์หลังเหตุการณ์ที่ถูกอ้างอิงบ่อย: **การบริหาร
  จัดการระบายน้ำเขื่อนที่เหมาะสมเพียงอย่างเดียว — ไม่ต้องสร้างสิ่งก่อสร้าง
  ใหม่ — น่าจะลดอัตราการระบายสูงสุดได้ราว 400 ลูกบาศก์เมตร/วินาที** พูดอีก
  แบบคือ ความเสียหายส่วนใหญ่ที่มีนัยสำคัญเป็นความล้มเหลวด้านการสนับสนุนการ
  ตัดสินใจและการประสานงาน ไม่ใช่ความหลีกเลี่ยงไม่ได้ทางอุทกวิทยาล้วน ๆ นี่คือ
  ข้อโต้แย้งที่หนักแน่นที่สุดสำหรับการสร้างเครื่องมือบูรณาการแบบนี้
- 65 จาก 77 จังหวัดของไทยได้รับผลกระทบ นิคมอุตสาหกรรมหลัก 7 แห่งในพื้นที่
  อยุธยา/ปทุมธานีจมน้ำ กระทบห่วงโซ่อุปทานโลกของฮาร์ดดิสก์ไดรฟ์และชิ้นส่วน
  ยานยนต์นานหลายเดือน

## The institutional landscape / ภูมิทัศน์เชิงสถาบัน

**EN.** Thailand's flood-management responsibility is split across roughly
**seven ministries and on the order of 48 agencies**, each maintaining its
own data systems, update cadences, and authentication schemes. The
practical consequence, observed repeatedly: no single authority controls
end-to-end flood data, and a unified dashboard must function as an
**integration layer above this fragmentation**, not assume one exists
already. The principal players a builder should know:

- **HII / HAII (Hydro-Informatics/Hydro and Agro Informatics Institute)** —
  operates the most comprehensive single access point (ThaiWater), with a
  national Decision Support System built in partnership with a Danish
  hydrodynamic-modeling firm, delivering roughly 7-day flood forecasts.
  Its forecast coverage is estimated at roughly **73% of Thai territory**,
  leaving a real gap in mountainous border regions and southern-peninsula
  provinces where a structurally different monsoon regime applies.
- **RID (Royal Irrigation Department)** — irrigation infrastructure, dam
  and canal-gate operations, its own Smart Water Management platform
  running HEC-HMS/HEC-RAS style modeling.
- **TMD (Thai Meteorological Department)** — ground weather observation
  network and short-range numerical weather prediction, the single best
  public rainfall-forecast product for the country.
- **GISTDA** — satellite-derived flood-extent mapping using radar
  (all-weather, cloud-penetrating), plus a high-resolution optical
  satellite for post-event assessment.
- **DDPM (Department of Disaster Prevention and Mitigation)** and its
  National Disaster Warning Center — operates the physical siren-tower
  network and cell-broadcast warning infrastructure.
- **EGAT (Electricity Generating Authority)** — operates the country's
  largest hydroelectric dams; its real-time telemetry has historically been
  harder to access programmatically than a simple REST feed.

**A well-documented cautionary case study**: a large mobile early-warning
application, procured at a cost on the order of USD 10+ million, was later
assessed by a national audit body as having failed to reach the
communities it was built to protect — cited causes included poor interface
design and lack of integration with provincial-level emergency workflows.
The lesson for anyone taking up this challenge: **procurement scale is not
a substitute for integration discipline**, and a lean, well-integrated
system built on free public data can outperform an expensive, poorly
integrated one.

**TH.** ความรับผิดชอบด้านการจัดการน้ำท่วมของไทยกระจายอยู่ในราว **เจ็ด
กระทรวงและประมาณ 48 หน่วยงาน** แต่ละที่ดูแลระบบข้อมูล ความถี่อัปเดต และ
รูปแบบการยืนยันตัวตนของตัวเอง ผลที่เกิดขึ้นจริงซึ่งสังเกตได้ซ้ำ ๆ คือ ไม่มี
หน่วยงานเดียวที่ควบคุมข้อมูลน้ำท่วมแบบครบวงจร และแดชบอร์ดรวมต้องทำหน้าที่
เป็น **ชั้นบูรณาการเหนือความกระจัดกระจายนี้** ไม่ใช่สมมติว่ามีอยู่แล้ว
ผู้เล่นหลักที่ผู้สร้างควรรู้จัก:

- **สสน./สทอภ. (สถาบันสารสนเทศทรัพยากรน้ำ)** — เป็นจุดเข้าถึงเดียวที่ครบ
  ที่สุด (ThaiWater) มีระบบสนับสนุนการตัดสินใจระดับชาติที่พัฒนาร่วมกับบริษัท
  แบบจำลองอุทกพลศาสตร์จากเดนมาร์ก ให้พยากรณ์น้ำท่วมล่วงหน้าประมาณ 7 วัน
  ความครอบคลุมการพยากรณ์ประมาณ **73% ของพื้นที่ไทย** ยังมีช่องว่างจริงใน
  พื้นที่ภูเขาชายแดนและจังหวัดภาคใต้ตอนล่างที่มีระบบมรสุมต่างออกไปโดย
  โครงสร้าง
- **กรมชลประทาน (RID)** — โครงสร้างพื้นฐานชลประทาน การดำเนินงานเขื่อนและ
  ประตูระบายน้ำคลอง มีแพลตฟอร์ม Smart Water Management ของตัวเองที่ใช้
  แบบจำลองสไตล์ HEC-HMS/HEC-RAS
- **กรมอุตุนิยมวิทยา (TMD)** — เครือข่ายตรวจอากาศภาคพื้นดินและพยากรณ์อากาศ
  เชิงตัวเลขระยะสั้น เป็นผลิตภัณฑ์พยากรณ์ฝนสาธารณะที่ดีที่สุดของประเทศ
- **สทอภ./GISTDA** — แผนที่ขอบเขตน้ำท่วมจากดาวเทียมโดยใช้เรดาร์ (ทุกสภาพ
  อากาศ ทะลุเมฆได้) บวกดาวเทียมออปติคัลความละเอียดสูงสำหรับประเมินหลัง
  เหตุการณ์
- **กรมป้องกันและบรรเทาสาธารณภัย (ปภ.)** และศูนย์เตือนภัยพิบัติแห่งชาติ —
  ดูแลเครือข่ายหอเตือนภัยจริงและโครงสร้างพื้นฐานแจ้งเตือนผ่านมือถือแบบ
  cell broadcast
- **การไฟฟ้าฝ่ายผลิตแห่งประเทศไทย (กฟผ./EGAT)** — บริหารเขื่อนไฟฟ้าพลังน้ำ
  ที่ใหญ่ที่สุดของประเทศ ข้อมูลโทรมาตรเรียลไทม์เข้าถึงผ่านโปรแกรมได้ยากกว่า
  ฟีด REST ธรรมดาในอดีต

**กรณีศึกษาเตือนใจที่มีการบันทึกไว้ดี**: แอปพลิเคชันเตือนภัยล่วงหน้าผ่านมือ
ถือขนาดใหญ่ ที่จัดซื้อด้วยงบประมาณระดับกว่า 10 ล้านดอลลาร์สหรัฐ ภายหลังถูก
หน่วยงานตรวจสอบระดับชาติประเมินว่าไม่สามารถเข้าถึงชุมชนที่ตั้งใจปกป้องได้
สาเหตุที่ระบุรวมถึงการออกแบบอินเทอร์เฟซที่ไม่ดีและขาดการเชื่อมโยงกับขั้นตอน
การทำงานฉุกเฉินระดับจังหวัด บทเรียนสำหรับใครก็ตามที่รับความท้าทายนี้: **ขนาด
การจัดซื้อไม่ใช่สิ่งทดแทนวินัยด้านการบูรณาการ** และระบบที่กระชับ บูรณาการดี
สร้างจากข้อมูลสาธารณะฟรี สามารถทำงานได้ดีกว่าระบบราคาแพงที่บูรณาการไม่ดีได้

## Monsoon physics and regional variation / ฟิสิกส์มรสุมและความแตกต่างระดับภูมิภาค

**EN.** Thailand's flood risk is not uniform in time or place, because two
distinct monsoon systems govern it:

- **The southwest monsoon (roughly May–October)** delivers on the order of
  **75% of the country's annual rainfall**, driving the Chao Phraya basin's
  flood season (peak river levels typically September–October, as
  accumulated northern rainfall propagates downstream over the multi-week
  timescale described above).
- **The northeast monsoon (roughly November–February)** brings the
  opposite pattern to the **Gulf-coast southern peninsula**: while the rest
  of the country is entering its dry season, cold northeasterly winds pick
  up moisture crossing the Gulf of Thailand and dump it on the east-facing
  coast — the mechanism behind the Hat Yai floods (Songkhla's Khlong
  U-Taphao basin), which recur almost every northeast-monsoon season
  because the catchment is small, steep, and fast-responding (flash-flood
  character, hours not days).
- **Climate projections** (from mainland-Southeast-Asia-focused studies)
  suggest that continued warming could increase the frequency of extreme
  precipitation events by roughly 20–30% by mid-century, alongside
  continuing land subsidence in Bangkok (on the order of 1–2 cm/year from
  groundwater extraction) that progressively reduces the gravity-drainage
  gradient available to the city's polder system.

**TH.** ความเสี่ยงน้ำท่วมของไทยไม่สม่ำเสมอทั้งเวลาและพื้นที่ เพราะมีระบบ
มรสุมสองแบบที่แตกต่างกันควบคุมอยู่:

- **มรสุมตะวันตกเฉียงใต้ (ราวพฤษภาคม–ตุลาคม)** ให้ฝนประมาณ **75% ของ
  ปริมาณฝนรายปีทั้งประเทศ** ขับเคลื่อนฤดูน้ำท่วมของลุ่มเจ้าพระยา (ระดับน้ำ
  แม่น้ำสูงสุดมักอยู่ช่วงกันยายน–ตุลาคม เมื่อฝนสะสมทางเหนือไหลลงมาตามช่วง
  เวลาหลายสัปดาห์ที่กล่าวข้างต้น)
- **มรสุมตะวันออกเฉียงเหนือ (ราวพฤศจิกายน–กุมภาพันธ์)** นำรูปแบบตรงข้ามมาสู่
  **คาบสมุทรภาคใต้ฝั่งอ่าวไทย** — ขณะที่ภาคอื่นของประเทศเข้าสู่ฤดูแล้ง ลมหนาว
  ตะวันออกเฉียงเหนือพัดผ่านอ่าวไทยรับความชื้นแล้วทิ้งลงฝั่งตะวันออกของคาบสมุทร
  — กลไกเบื้องหลังน้ำท่วมหาดใหญ่ (ลุ่มคลองอู่ตะเภา จ.สงขลา) ที่เกิดซ้ำเกือบ
  ทุกฤดูมรสุมตะวันออกเฉียงเหนือ เพราะลุ่มน้ำเล็ก ลาดชัน ตอบสนองเร็ว (ลักษณะ
  น้ำท่วมฉับพลัน หน่วยเป็นชั่วโมงไม่ใช่วัน)
- **การคาดการณ์ภูมิอากาศ** (จากงานศึกษาที่เน้นแผ่นดินใหญ่เอเชียตะวันออกเฉียง
  ใต้) ชี้ว่าภาวะโลกร้อนต่อเนื่องอาจเพิ่มความถี่ของเหตุการณ์ฝนตกหนักผิดปกติ
  ราว 20–30% ภายในกลางศตวรรษ ควบคู่กับการทรุดตัวของแผ่นดินกรุงเทพฯ ที่ยังคง
  ดำเนินอยู่ (ราว 1–2 ซม./ปี จากการสูบน้ำบาดาล) ที่ลดความชันสำหรับการระบายน้ำ
  แบบแรงโน้มถ่วงของระบบ polder ของเมืองลงเรื่อย ๆ

## International comparison, briefly / เทียบกับนานาชาติโดยสังเขป

**EN.** Bangladesh's Flood Forecasting and Warning Centre operates as a
**single national authority** issuing standardized deterministic and
probabilistic forecasts — a structural contrast to Thailand's
multi-agency model, and a reminder that **institutional unification**, not
just more sensors, is often the highest-leverage fix elsewhere too. Japan's
X-band radar network (very high spatial and temporal resolution, under a
unified meteorological authority) represents the other end of the
investment spectrum — instructive as a target state, not a realistic
starting point for most teams taking up this challenge.

**TH.** ศูนย์พยากรณ์และเตือนภัยน้ำท่วมของบังกลาเทศดำเนินงานเป็น
**หน่วยงานแห่งชาติเดียว** ออกพยากรณ์แบบกำหนดค่าและความน่าจะเป็นที่เป็น
มาตรฐาน — ตัดกันเชิงโครงสร้างกับโมเดลหลายหน่วยงานของไทย และเป็นเครื่องเตือน
ใจว่า **การรวมศูนย์เชิงสถาบัน** ไม่ใช่แค่เซนเซอร์มากขึ้น มักเป็นทางแก้ที่คุ้ม
ค่าที่สุดในที่อื่นด้วยเช่นกัน เครือข่ายเรดาร์ย่าน X ของญี่ปุ่น (ความละเอียด
เชิงพื้นที่และเวลาสูงมาก ภายใต้หน่วยงานอุตุนิยมวิทยาเดียว) แทนปลายอีกด้านของ
สเปกตรัมการลงทุน — เป็นตัวอย่างสถานะเป้าหมาย ไม่ใช่จุดเริ่มต้นที่เป็นจริง
สำหรับทีมส่วนใหญ่ที่รับความท้าทายนี้

---

[← Deep-dive index](README.md) · [Next →](02-data-sources-deep-dive.md)
