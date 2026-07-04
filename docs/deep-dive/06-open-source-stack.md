# Deep dive 6: The open-source tool registry
## เจาะลึก 6: ทะเบียนเครื่องมือโอเพนซอร์ส

[← Previous](05-risk-scoring-frameworks.md) · [Deep-dive index](README.md) · [Next →](07-alert-systems.md)

---

**EN.** FloodDash itself deliberately runs on almost no dependencies — a
single Node.js process, built-in SQLite, vendored Leaflet, no build step
(see [architecture](../02-architecture.md)). That minimalism was a choice
suited to one operator running one instance on one Mac. If your challenge
attempt needs to scale further — multiple regions, a team of contributors,
heavier hydrological modeling — the wider open-source flood-tech ecosystem
has mature, permissively-licensed options for nearly every layer. This
document is a registry of what exists, so you can make an informed
build-vs-adopt decision rather than reinventing something that already has
a maintained implementation.

**TH.** FloodDash เองตั้งใจรันด้วย dependency แทบไม่มีเลย — โปรเซส Node.js
เดี่ยว SQLite ในตัว Leaflet ที่ vendor ไว้ ไม่มีขั้นตอน build (ดู
[สถาปัตยกรรม](../02-architecture.md)) ความเรียบง่ายนั้นเป็นทางเลือกที่
เหมาะกับผู้ปฏิบัติงานคนเดียวรันหนึ่งอินสแตนซ์บน Mac หนึ่งเครื่อง ถ้าความ
พยายามในความท้าทายของคุณต้องขยายไปไกลกว่านั้น — หลายภูมิภาค ทีมผู้ร่วมสมทบ
แบบจำลองอุทกวิทยาที่หนักขึ้น — ระบบนิเวศเทคโนโลยีน้ำท่วมโอเพนซอร์สที่กว้าง
กว่ามีตัวเลือกที่เป็นผู้ใหญ่และมีสัญญาอนุญาตแบบเสรีสำหรับเกือบทุกชั้น
เอกสารนี้คือทะเบียนของสิ่งที่มีอยู่ เพื่อให้คุณตัดสินใจสร้างเองหรือรับมาใช้
อย่างมีข้อมูล แทนที่จะประดิษฐ์สิ่งที่มีการทำไว้แล้วซ้ำ

## Hydrological modeling engines / เครื่องมือแบบจำลองอุทกวิทยา

| Tool | License | What it does | Notes |
|---|---|---|---|
| **Wflow.jl** (Deltares) | MIT | Distributed rainfall-runoff modeling in Julia, designed for operational forecasting pipelines | Actively maintained by a national hydraulic-engineering institute; strong choice if your team has any Julia comfort |
| **LISFLOOD-FP** | GPL v3 | 2D hydrodynamic flood-inundation modeling, widely used in academic and humanitarian flood mapping | GPL means derivative distribution obligations — check compatibility with your license plans |
| **RRI Model** (Rainfall-Runoff-Inundation, ICHARM/Japan) | Free for research/non-commercial use | Coupled 2D rainfall-runoff + inundation, purpose-built for and validated against monsoon-Asia catchments including Thai basins | Not a fully open OSI license — read the specific terms before commercial use |
| **SFINCS** (Deltares) | GPL v3 | Reduced-complexity (fast) compound-flooding model — river + rainfall + coastal storm surge together | Popular where a full 2D hydrodynamic model like HEC-RAS is too slow for near-real-time operational use |
| **HEC-RAS / HEC-HMS** (US Army Corps of Engineers) | Free, public-domain (US government work) | The global standard 1D/2D hydraulic and hydrologic modeling suite | Not "open source" in the contributor sense (closed development, no public repo) but free to use and redistribute; has a scripting/automation API |

**TH.** ดูตารางด้านบนสำหรับเอนจินแบบจำลองอุทกวิทยา — Wflow.jl (MIT, การ
ไหลบ่าน้ำฝนแบบกระจายจาก Deltares), LISFLOOD-FP (GPL v3, แบบจำลองน้ำท่วม
2 มิติ), RRI Model (ฟรีสำหรับงานวิจัย ปรับเทียบกับลุ่มน้ำมรสุมเอเชียรวมถึง
ไทย), SFINCS (GPL v3 แบบจำลองน้ำท่วมผสมที่รวดเร็ว), HEC-RAS/HEC-HMS
(สาธารณสมบัติจากรัฐบาลสหรัฐ มาตรฐานโลก)

## Remote-sensing and satellite-analysis toolkits / ชุดเครื่องมือวิเคราะห์ภาพถ่ายดาวเทียม

**EN.** **HYDRAFloods** (open-source, Python, built on Google Earth Engine)
is the most directly relevant tool here: it automates the exact satellite
flood-mapping workflow described in [deep dive 2](02-data-sources-deep-dive.md)
— fetching, cloud-masking, and thresholding radar and optical imagery to
produce flood-extent rasters — as a maintained pipeline rather than
hand-rolled scripts. It depends on Earth Engine access (free for
research/nonprofit use, requires registration). For teams not using Earth
Engine, the raw processing can be replicated with **SNAP** (ESA's free
Sentinel Application Platform, GPL) for radar preprocessing and standard
Python geospatial libraries (`rasterio`, `xarray`, `rioxarray` — all
BSD/MIT-family licensed) for the thresholding and masking logic itself.

**TH.** **HYDRAFloods** (โอเพนซอร์ส Python สร้างบน Google Earth Engine)
คือเครื่องมือที่เกี่ยวข้องโดยตรงที่สุดในที่นี้: มันทำให้ขั้นตอนการทำแผนที่
น้ำท่วมจากดาวเทียมที่อธิบายใน[เจาะลึก 2](02-data-sources-deep-dive.md)เป็น
อัตโนมัติ — ดึงข้อมูล ปิดบังเมฆ และตั้งเกณฑ์ภาพเรดาร์และภาพแสงเพื่อสร้าง
raster ขอบเขตน้ำท่วม — เป็นไปป์ไลน์ที่ดูแลรักษาแทนสคริปต์เขียนมือ มันต้อง
พึ่ง Earth Engine (ฟรีสำหรับงานวิจัย/ไม่แสวงกำไร ต้องลงทะเบียน) สำหรับทีม
ที่ไม่ใช้ Earth Engine การประมวลผลดิบสามารถทำซ้ำได้ด้วย **SNAP** (แพลตฟอร์ม
ประมวลผล Sentinel ฟรีของ ESA, GPL) สำหรับการเตรียมภาพเรดาร์ และไลบรารี
geospatial Python มาตรฐาน (`rasterio`, `xarray`, `rioxarray` — สัญญาอนุญาต
ตระกูล BSD/MIT ทั้งหมด) สำหรับตรรกะตั้งเกณฑ์และปิดบังเอง

## Operational forecasting platforms (full-stack references) / แพลตฟอร์มพยากรณ์ปฏิบัติการ (การอ้างอิงแบบเต็มสแตก)

**EN.** Two mature open platforms show what a fully-scaled version of
FloodDash's own scheduler-adapter-database-bus-presentation pattern
(see [architecture](../02-architecture.md)) looks like at institutional
scale:

- **Delft-FEWS** (Deltares Flood Early Warning System) — the platform
  behind many national/regional operational flood-forecasting centers
  worldwide. Open-source, Java-based, designed explicitly around
  plug-in "modules" for different data sources and models — architecturally
  the same adapter pattern FloodDash uses, just far more configurable and
  enterprise-oriented. Worth studying its module-configuration XML schema
  even if you don't adopt the platform itself, as a reference for how a
  mature system handles multi-source-multi-model orchestration.
- **Tethys Platform** (Aquaveo/BYU) — an open-source Django-based
  framework specifically for building water-resources web apps, with
  built-in support for map visualization, job scheduling for model runs,
  and app-store-style packaging of individual water-modeling tools as
  installable "Tethys apps." A strong fit if your team is more
  Python/Django-fluent than Node-fluent and wants a batteries-included
  starting point rather than FloodDash's from-scratch approach.

**TH.** สองแพลตฟอร์มโอเพนซอร์สที่เป็นผู้ใหญ่แสดงให้เห็นว่ารูปแบบ scheduler-
adapter-database-bus-presentation ของ FloodDash เองที่ขยายเต็มสเกลจะเป็น
อย่างไรในระดับสถาบัน — **Delft-FEWS** (แพลตฟอร์มเบื้องหลังศูนย์พยากรณ์น้ำ
ท่วมปฏิบัติการระดับชาติ/ภูมิภาคหลายแห่งทั่วโลก โอเพนซอร์ส ใช้ Java ออกแบบ
รอบโมดูล plug-in) และ **Tethys Platform** (เฟรมเวิร์ก Django โอเพนซอร์ส
สำหรับสร้างเว็บแอปทรัพยากรน้ำ พร้อมการสนับสนุนในตัวสำหรับการแสดงผลแผนที่
การจัดตารางงานรันแบบจำลอง)

## Data infrastructure / โครงสร้างพื้นฐานข้อมูล

| Layer | Lightweight (FloodDash's choice) | Scaled-up alternative | When to graduate |
|---|---|---|---|
| Time-series storage | SQLite (built into Node.js) | **TimescaleDB** (PostgreSQL extension, Apache 2.0/Timescale license) | When a single machine's disk I/O or concurrent-write load becomes the bottleneck, or when multiple services need concurrent write access |
| Event streaming | In-process event bus + SSE ring buffer | **Apache Kafka** or **Redpanda** (Kafka-API-compatible, BSL/Apache-mixed license) | When you have multiple independent consumers (e.g., a separate alerting service, a separate analytics pipeline) that shouldn't share one process's memory |
| API layer | Hand-rolled Node.js routes | **FastAPI** (Python, MIT) or keep Node with **Fastify** (MIT) | When you need OpenAPI schema generation, request validation, or a Python-native team |
| Map rendering | Leaflet (BSD-2-Clause) | **MapLibre GL JS** (BSD-3-Clause, the open fork of Mapbox GL after Mapbox's license change) | When you need vector-tile rendering, 3D terrain, or GPU-accelerated large-dataset rendering that raster Leaflet layers can't handle smoothly |

**TH.** ดูตารางด้านบนสำหรับโครงสร้างพื้นฐานข้อมูล — การเก็บอนุกรมเวลา
(SQLite → TimescaleDB), การส่งผ่านเหตุการณ์ (event bus ในตัว → Kafka/
Redpanda), ชั้น API (Node.js มือเขียน → FastAPI/Fastify), การเรนเดอร์แผนที่
(Leaflet → MapLibre GL JS)

## Reference cost economics / เศรษฐศาสตร์ต้นทุนอ้างอิง

**EN.** For teams estimating cloud costs beyond FloodDash's own
zero-marginal-cost, single-Mac-plus-Cloudflare-tunnel design (see
[roadmap](../06-build-your-own-roadmap.md)): a small managed PostgreSQL/
TimescaleDB instance suitable for a province-scale deployment runs
roughly $15–50/month on any major cloud provider's smallest managed-database
tier; a single small compute instance for the scheduler/API layer runs
similarly in the $10–40/month range; object storage for satellite imagery
archives is usage-based and typically negligible at this scale (a few
dollars/month) unless you're archiving full-resolution SAR scenes at high
frequency. **(single-source estimate** — these are illustrative
order-of-magnitude figures from general cloud-pricing knowledge, not a
quote from any specific vendor; get current pricing before budgeting.)
The single-Mac-plus-tunnel approach FloodDash itself uses remains the
cheapest possible path for a v1 and should not be abandoned prematurely —
graduate to managed cloud infrastructure only when a concrete scaling
pressure (multiple concurrent regions, a team needing shared write access,
uptime SLAs beyond what a home Mac can offer) actually appears.

**TH.** สำหรับทีมที่ประเมินต้นทุนคลาวด์เกินกว่าการออกแบบต้นทุนส่วนเพิ่มเป็น
ศูนย์ของ FloodDash เอง (Mac เดียวบวก Cloudflare tunnel ดู
[แผนงาน](../06-build-your-own-roadmap.md)): อินสแตนซ์ PostgreSQL/TimescaleDB
ที่จัดการขนาดเล็กที่เหมาะกับการใช้งานระดับจังหวัดรันประมาณ 15-50 ดอลลาร์/
เดือนบนผู้ให้บริการคลาวด์รายใหญ่ระดับเล็กที่สุด อินสแตนซ์คำนวณเดี่ยวขนาด
เล็กสำหรับชั้น scheduler/API รันในช่วงใกล้เคียงกัน 10-40 ดอลลาร์/เดือน
ที่เก็บวัตถุสำหรับคลังภาพดาวเทียมคิดตามการใช้งานและมักจะน้อยมากในสเกลนี้
**(ประมาณการแหล่งเดียว** — ตัวเลขเหล่านี้เป็นลำดับขนาดประกอบการอธิบายจาก
ความรู้ราคาคลาวด์ทั่วไป ไม่ใช่ใบเสนอราคาจากผู้ขายเฉพาะเจาะจง ตรวจสอบราคา
ปัจจุบันก่อนตั้งงบประมาณ) แนวทาง Mac เดียวบวก tunnel ที่ FloodDash เองใช้
ยังคงเป็นเส้นทางที่ถูกที่สุดสำหรับ v1 และไม่ควรละทิ้งก่อนเวลาอันควร —
ขยับไปใช้โครงสร้างพื้นฐานคลาวด์ที่จัดการเมื่อมีแรงกดดันการขยายที่เป็นรูป
ธรรมจริง ๆ เท่านั้น

---

[← Previous](05-risk-scoring-frameworks.md) · [Deep-dive index](README.md) · [Next →](07-alert-systems.md)
