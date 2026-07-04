# Deep dive 2: Data sources, endpoint by endpoint
## เจาะลึก 2: แหล่งข้อมูลทีละจุดเชื่อมต่อ

[← Previous](01-thailand-flood-context.md) · [Deep-dive index](README.md) · [Next →](03-hydrological-models.md)

---

**EN.** This expands the main blueprint's [data sources catalog](../03-data-sources.md)
with the field-level and query-level detail a builder actually needs when
writing the first fetch call.

**TH.** เอกสารนี้ขยายจาก[แคตตาล็อกแหล่งข้อมูล](../03-data-sources.md)ของ
พิมพ์เขียวหลัก ด้วยรายละเอียดระดับฟิลด์และระดับคำสั่งคิวรีที่ผู้สร้างต้องใช้
จริงตอนเขียนการดึงข้อมูลครั้งแรก

## The national telemetry backbone / กระดูกสันหลังโทรมาตรระดับชาติ

**EN.** Thailand's national hydrological telemetry aggregator exposes
dozens of REST endpoints without authentication, organized by domain.
The categories worth knowing:

- **Rainfall endpoints** return 1-hour and 24-hour accumulation per gauge,
  plus multi-day forecast summaries, filterable by province or basin code.
- **Water-level endpoints** return the current stage in metres above mean
  sea level, a categorical situation/severity level, and — critically —
  **per-station warning and critical thresholds**, so you don't have to
  invent your own thresholds from scratch; the agency has already
  calibrated them per gauge.
- **A historical time-series endpoint** exists per station (accepting a
  station code and a date range) — invaluable for backfilling history and
  for calibrating your own risk-score thresholds against real past events.
- **Dam/reservoir endpoints** return storage in million cubic metres,
  inflow, release, and reservoir level, though field names and units are
  not always consistent across the different contributing agencies feeding
  into the same aggregator.
- **Canal/floodgate endpoints** cover urban drainage infrastructure (gate
  positions, pump status) for the capital region specifically.
- Basins and provinces are addressed by short numeric codes in most query
  parameters (worth building a lookup table early rather than hardcoding
  a handful of codes you happen to need first).

**Two gotchas worth inheriting** (see also §3.3 of the main blueprint):

1. Real-time rows frequently carry a **quality flag indicating
   "unverified"/automated-range-checked-only**, not human-verified. Build a
   simple staleness/deviation scoring layer: flag any station that hasn't
   updated within roughly double its expected cadence, and flag readings
   that deviate sharply from neighboring stations in the same basin.
2. A meaningful fraction of the national telemetry network (on the order
   of **a third of stations**, by one internal assessment) has periods of
   **data-continuity gaps exceeding 30 days** in a given year, concentrated
   in remote mountainous terrain where maintenance access during the wet
   season is hardest — exactly the terrain where flash floods originate.
   Don't assume uniform data quality across the network; weight or
   backfill accordingly.

**TH.** ระบบรวบรวมโทรมาตรอุทกวิทยาแห่งชาติของไทยเปิด REST endpoint หลายสิบ
จุดโดยไม่ต้องยืนยันตัวตน จัดกลุ่มตามหมวดข้อมูล หมวดที่ควรรู้จัก:

- **จุดเชื่อมต่อฝน** คืนค่าฝนสะสม 1 ชม. และ 24 ชม. ต่อสถานี พร้อมสรุป
  พยากรณ์หลายวัน กรองตามรหัสจังหวัดหรือลุ่มน้ำได้
- **จุดเชื่อมต่อระดับน้ำ** คืนค่าระดับปัจจุบันเป็นเมตรเหนือระดับน้ำทะเลปาน
  กลาง ระดับสถานการณ์/ความรุนแรงเชิงหมวดหมู่ และ — สำคัญมาก — **เกณฑ์เตือน
  ภัยและเกณฑ์วิกฤตต่อสถานี** ทำให้ไม่ต้องคิดเกณฑ์ของตัวเองจากศูนย์ หน่วยงาน
  ปรับเทียบไว้ให้ต่อมาตรวัดแล้ว
- มี **จุดเชื่อมต่ออนุกรมเวลาย้อนหลัง** ต่อสถานี (รับรหัสสถานีและช่วงวันที่)
  — มีค่ามากสำหรับดึงข้อมูลย้อนหลังและปรับเทียบเกณฑ์คะแนนความเสี่ยงของ
  ตัวเองกับเหตุการณ์จริงในอดีต
- **จุดเชื่อมต่อเขื่อน/อ่างเก็บน้ำ** คืนค่าปริมาณกักเก็บเป็นล้านลูกบาศก์เมตร
  น้ำไหลเข้า ระบายออก และระดับอ่าง แม้ชื่อฟิลด์และหน่วยไม่สม่ำเสมอเสมอไป
  ระหว่างหน่วยงานต่าง ๆ ที่ป้อนข้อมูลเข้าระบบรวมเดียวกัน
- **จุดเชื่อมต่อคลอง/ประตูระบายน้ำ** ครอบคลุมโครงสร้างพื้นฐานระบายน้ำในเมือง
  (ตำแหน่งประตู สถานะปั๊ม) เฉพาะพื้นที่เมืองหลวง
- ลุ่มน้ำและจังหวัดอ้างด้วยรหัสตัวเลขสั้นในพารามิเตอร์คิวรีส่วนใหญ่ (คุ้มค่า
  ที่จะสร้างตารางค้นหาแต่เนิ่น ๆ แทนที่จะ hardcode รหัสไม่กี่ตัวที่ต้องใช้
  ก่อน)

**สองข้อควรระวังที่คุ้มค่าจะรับไว้** (ดูหัวข้อ 3.3 ของพิมพ์เขียวหลักด้วย):

1. แถวเรียลไทม์มักมี **ป้ายคุณภาพที่บ่งบอกว่า "ยังไม่ยืนยัน"/ตรวจสอบช่วงค่า
   อัตโนมัติเท่านั้น** ไม่ใช่มนุษย์ยืนยันแล้ว สร้างชั้นให้คะแนนความเก่า/ความ
   เบี่ยงเบนง่าย ๆ: ติดธงสถานีที่ไม่อัปเดตภายในประมาณสองเท่าของความถี่ที่คาด
   และติดธงค่าที่เบี่ยงเบนมากจากสถานีข้างเคียงในลุ่มน้ำเดียวกัน
2. สัดส่วนที่มีนัยสำคัญของเครือข่ายโทรมาตรแห่งชาติ (ราว **หนึ่งในสามของ
   สถานี** ตามการประเมินภายในครั้งหนึ่ง) มีช่วง **ข้อมูลขาดต่อเนื่องเกิน 30
   วัน** ในปีใดปีหนึ่ง กระจุกตัวในพื้นที่ภูเขาห่างไกลที่การเข้าถึงเพื่อบำรุง
   รักษาช่วงฤดูฝนยากที่สุด — ตรงกับพื้นที่ที่น้ำท่วมฉับพลันเริ่มต้นพอดี อย่า
   สมมติว่าคุณภาพข้อมูลสม่ำเสมอทั่วเครือข่าย ถ่วงน้ำหนักหรือเติมข้อมูลย้อนหลัง
   ให้เหมาะสม

## The rest of the government catalog / แคตตาล็อกภาครัฐที่เหลือ

**EN.**

- **The meteorological department** runs two separate systems: a ground-
  observation API (station data, monthly climatology) and a **separate,
  higher-resolution numerical weather prediction API** running nested
  domains — a coarser domain producing multi-day forecasts at 3-hour
  intervals, and a finer domain producing short-range hourly forecasts.
  The finer domain is the best public rainfall-forecast input available
  for driving a Thai rainfall-runoff model. Both require free registration.
- **The satellite mapping agency** exposes flood-extent layers through
  standard OGC map-service protocols (so they drop straight into any
  Leaflet/MapLibre/OpenLayers map), at multiple temporal aggregation
  windows (1-day through 30-day), plus a very-high-resolution optical
  satellite (sub-metre) usable for post-event damage assessment via
  tasking request rather than an open feed.
- **The irrigation department's reservoir API** is genuinely simple:
  one endpoint for current data, one parameterized-by-date endpoint for
  history, no authentication.
- **The historical-hydrology archive** (a separate portal from the
  real-time telemetry aggregator) sells bulk historical exports at a
  nominal per-station-per-year fee — cheap enough to be a rounding error
  for a serious project, useful for calibrating return-period statistics
  (see Deep Dive 3).

**TH.**

- **กรมอุตุนิยมวิทยา** รันสองระบบแยกกัน: API ตรวจอากาศภาคพื้นดิน (ข้อมูล
  สถานี ภูมิอากาศวิทยารายเดือน) และ **API พยากรณ์อากาศเชิงตัวเลขความละเอียด
  สูงแยกต่างหาก** รันโดเมนซ้อนกัน — โดเมนหยาบให้พยากรณ์หลายวันทุก 3 ชั่วโมง
  และโดเมนละเอียดให้พยากรณ์รายชั่วโมงระยะสั้น โดเมนละเอียดเป็นข้อมูลนำเข้า
  พยากรณ์ฝนสาธารณะที่ดีที่สุดสำหรับขับเคลื่อนแบบจำลองฝน-น้ำท่าของไทย ทั้งคู่
  ต้องลงทะเบียนฟรี
- **หน่วยงานแผนที่ดาวเทียม** เปิดชั้นขอบเขตน้ำท่วมผ่านโพรโทคอลบริการแผนที่
  มาตรฐาน OGC (เสียบเข้า Leaflet/MapLibre/OpenLayers ได้ตรง ๆ) หลายหน้าต่าง
  รวมเวลา (1 วันถึง 30 วัน) บวกดาวเทียมออปติคัลความละเอียดสูงมาก (ต่ำกว่า
  เมตร) ใช้ประเมินความเสียหายหลังเหตุการณ์ผ่านคำขอ tasking แทนที่จะเป็นฟีด
  เปิด
- **API อ่างเก็บน้ำของกรมชลประทาน** เรียบง่ายจริง ๆ: จุดเชื่อมต่อเดียวสำหรับ
  ข้อมูลปัจจุบัน จุดเชื่อมต่อพารามิเตอร์วันที่สำหรับประวัติ ไม่ต้องยืนยันตัวตน
- **คลังอุทกวิทยาประวัติศาสตร์** (พอร์ทัลแยกจากระบบรวมโทรมาตรเรียลไทม์) ขาย
  การส่งออกข้อมูลย้อนหลังจำนวนมากในราคาเล็กน้อยต่อสถานีต่อปี — ถูกพอที่จะเป็น
  ตัวเลขปัดเศษสำหรับโครงการจริงจัง มีประโยชน์สำหรับปรับเทียบสถิติคาบการเกิดซ้ำ
  (ดูเจาะลึก 3)

## International sources worth batching / แหล่งข้อมูลนานาชาติที่ควรจัดกลุ่มดึง

**EN.** A free global weather-forecast API that accepts **many coordinate
pairs in a single request** lets you fetch a 48-hour forecast for every
province centroid in the country in one HTTP call — vastly better than 77
separate requests. The same provider wraps the Copernicus global river-
discharge reanalysis/forecast system, giving daily-updated discharge
(including ensemble members) at 5 km global grid resolution with a
multi-week forecast horizon, entirely keyless. A free animated-radar API
provides recent-past and short-nowcast precipitation tiles suitable for
direct map-layer use (real data typically only to a modest zoom level;
beyond that the tile server returns a placeholder, so cap your zoom
accordingly). A public-domain, gauge-plus-satellite-blended rainfall
archive going back to the early 1980s at ~5 km resolution is the standard
choice for building long-term climatology and drought/flood anomaly
baselines.

**TH.** API พยากรณ์อากาศโลกฟรีที่รับ **พิกัดหลายคู่ในคำขอเดียว** ทำให้ดึง
พยากรณ์ 48 ชั่วโมงของทุกจุดศูนย์กลางจังหวัดทั้งประเทศได้ในคำขอ HTTP เดียว —
ดีกว่าการยิง 77 คำขอแยกกันมาก ผู้ให้บริการรายเดียวกันห่อระบบวิเคราะห์ย้อนหลัง/
พยากรณ์อัตราการไหลแม่น้ำโลกของ Copernicus ให้อัตราการไหลอัปเดตรายวัน (รวม
สมาชิกกลุ่มตัวอย่าง) ที่ความละเอียดกริดโลก 5 กม. พร้อมขอบฟ้าพยากรณ์หลาย
สัปดาห์ ไม่ต้องใช้คีย์เลย API เรดาร์ฝนเคลื่อนไหวฟรีให้ไทล์ฝนย้อนหลังใกล้และ
nowcast ระยะสั้นที่ใช้เป็นเลเยอร์แผนที่ได้ตรง ๆ (ข้อมูลจริงมักมีแค่ถึงระดับ
ซูมปานกลาง เกินกว่านั้นเซิร์ฟเวอร์ไทล์จะคืนภาพ placeholder ดังนั้นจำกัดระดับ
ซูมให้เหมาะสม) คลังฝนสาธารณสมบัติที่ผสมข้อมูลสถานีกับดาวเทียม ย้อนหลังไปถึง
ต้นทศวรรษ 1980 ที่ความละเอียดราว 5 กม. เป็นตัวเลือกมาตรฐานสำหรับสร้าง
ภูมิอากาศวิทยาระยะยาวและเส้นฐานความผิดปกติภัยแล้ง/น้ำท่วม

## Satellite flood-extent mapping, briefly / การทำแผนที่ขอบเขตน้ำท่วมจากดาวเทียมโดยสังเขป

**EN.** Radar satellites (synthetic-aperture radar, C-band) are the
workhorse for flood-extent mapping because their microwave wavelengths
penetrate monsoon cloud cover that blocks optical sensors for days or
weeks at a time. Flooded areas appear as a sharp drop in backscatter
(smooth water reflects the radar signal away from the sensor) — the
standard technique is a pre-flood-vs-post-flood image difference,
thresholded and masked against a permanent-water layer (derived from
decades of optical satellite history) so you don't misclassify a lake as
"new flooding." A public benchmark dataset with human-labeled flood
imagery across multiple continents exists for anyone wanting to train or
validate their own segmentation model; open pre-trained model weights for
this exact task are also publicly available.

**TH.** ดาวเทียมเรดาร์ (synthetic-aperture radar ย่าน C-band) เป็นตัวหลัก
สำหรับการทำแผนที่ขอบเขตน้ำท่วม เพราะความยาวคลื่นไมโครเวฟทะลุเมฆมรสุมที่บัง
เซนเซอร์ออปติคัลได้นานเป็นวันหรือสัปดาห์ พื้นที่น้ำท่วมปรากฏเป็นการลดลงอย่าง
ชัดเจนของ backscatter (น้ำเรียบสะท้อนสัญญาณเรดาร์ออกจากเซนเซอร์) เทคนิค
มาตรฐานคือเทียบภาพก่อน-หลังน้ำท่วม ตั้งเกณฑ์และมาสก์กับชั้นน้ำถาวร (สร้างจาก
ประวัติดาวเทียมออปติคัลหลายสิบปี) เพื่อไม่ให้จัดทะเลสาบเป็น "น้ำท่วมใหม่" ผิด
มีชุดข้อมูลมาตรฐานสาธารณะที่มนุษย์ติดป้ายภาพน้ำท่วมจากหลายทวีปสำหรับใครที่
อยากฝึกหรือตรวจสอบแบบจำลองแบ่งส่วนภาพของตัวเอง มีน้ำหนักแบบจำลองที่ฝึกไว้
แล้วแบบเปิดสำหรับงานนี้โดยเฉพาะเปิดเผยสาธารณะเช่นกัน

---

[← Previous](01-thailand-flood-context.md) · [Deep-dive index](README.md) · [Next →](03-hydrological-models.md)
