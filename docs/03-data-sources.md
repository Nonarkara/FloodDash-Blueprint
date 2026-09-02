# 3. The complete free data-source catalog / แคตตาล็อกแหล่งข้อมูลฟรีทั้งหมด

[← Architecture](02-architecture.md) · [Next: The Science →](04-the-science.md)

---

**EN.** Every source below is public, and every endpoint has been verified
live. None require a paid subscription. Some require free registration
(noted). This catalog alone should save a new team weeks of discovery work.

**TH.** ทุกแหล่งข้างล่างเป็นข้อมูลสาธารณะ และทุกจุดเชื่อมต่อได้ตรวจสอบว่าใช้
งานได้จริงแล้ว ไม่มีจุดใดต้องเสียเงิน บางจุดต้องลงทะเบียนฟรี (ระบุไว้)
แคตตาล็อกนี้อย่างเดียวก็ช่วยทีมใหม่ประหยัดเวลาค้นหาได้หลายสัปดาห์

## 3.1 Thai government sources / แหล่งข้อมูลภาครัฐไทย

| Source / แหล่ง | Agency | Access | Cadence | What it gives / ให้ข้อมูลอะไร |
|---|---|---|---|---|
| **ThaiWater v3 API** | HII — Hydro-Informatics Institute (สสน.) | `https://api-v3.thaiwater.net/api/v1/thaiwater30/public/waterlevel` — no key on `/public/` and `/analyst/` routes | 10–15 min | Water level (m MSL), % bank capacity, HII situation level (1 critically-low … 5 overflowing), across hundreds of telemetry stations nationwide. **Measured.** |
| **ThaiWater rain** | Multi-agency, aggregated via HII | `https://api-v3.thaiwater.net/api/v1/thaiwater30/public/rain_24h` | 10–15 min | 1-hour and 24-hour rainfall accumulation, thousands of gauges. **Measured.** |
| **ThaiWater dam feed** | HII / EGAT / RID | `https://api-v3.thaiwater.net/api/v1/thaiwater30/analyst/dam` | hourly–daily | Major dam storage (MCM), inflow, release. **Gotcha:** the feed mixes recent and years-old stale rows — filter by observation date before trusting a row. **Measured.** |
| **RID reservoir API** | Royal Irrigation Department (กรมชลประทาน) | `https://app.rid.go.th/reservoir/api/reservoir/public` — no key; date-specific variant at `/public/{YYYY-MM-DD}` | daily | Medium reservoir storage %, volume (MCM), for several hundred reservoirs nationwide. **Measured.** |
| **DWR early warning** | Department of Water Resources (กรมทรัพยากรน้ำ) | portal `https://ews.dwr.go.th` · stations `https://ews.dwr.go.th/ews/web-service/stn` | ~15 min | Community flash-flood / landslide stations with official alert status and, where reported, soil moisture. **Measured.** Independent of HII; especially useful in steep headwaters. |
| **Air4Thai** | Pollution Control Department (กรมควบคุมมลพิษ) | `https://air4thai.pcd.go.th/services/getNewAQI_JSON.php` — **must use HTTPS**; some HTTP clients reject its TLS chain, curl usually works | hourly | AQI and PM2.5 at ground stations nationwide. **Gotcha:** missing values are sent as `-1`, not `null` — filter them. **Measured.** |
| **TMD observations & NWP** | Thai Meteorological Department | portal `https://data.tmd.go.th/api/` · register `https://data.tmd.go.th/api/registerPre.php` (free `uid`/`ukey`); higher-resolution NWP separately registered | hourly (obs), 3-hourly (3 km NWP forecast) | Ground observations (**measured**) and short-range NWP (**modelled**) — the highest-quality public rain forecast for Thailand |
| **GISTDA flood map / Sphere** | GISTDA (สำนักงานพัฒนาเทคโนโลยีอวกาศและภูมิสารสนเทศ) | flood map `https://flood.gistda.or.th` · Sphere `https://sphere.gistda.or.th` | 1–7 day windows | Related public portals. Prefer the Disaster Platform row below for documented EO APIs. |
| **GISTDA Disaster Platform** | GISTDA — credit **GISTDA** on every layer | portal `https://disaster.gistda.or.th` · Open API Swagger `https://disaster.gistda.or.th/services/open-api` · gateway `https://api-gateway.gistda.or.th/api/2.0/resources` (**your** API key) | Flood WMS/WMTS/TMS 1/3/7/30-day + flood-freq; fire VIIRS/burn; drought DRI/NDWI/SMAP 7-day | Named public Thai EO source. Satellite-derived, not a river gauge — see paragraph below. |

**GISTDA Disaster Platform / แพลตฟอร์มภัยพิบัติ GISTDA.** **TH.** แหล่งสำรวจโลกสาธารณะของไทยที่ตั้งชื่อได้: พอร์ทัล [`disaster.gistda.or.th`](https://disaster.gistda.or.th) เอกสาร Open API (Swagger) [`/services/open-api`](https://disaster.gistda.or.th/services/open-api) และเกตเวย์ [`api-gateway.gistda.or.th/api/2.0/resources`](https://api-gateway.gistda.or.th/api/2.0/resources) (ต้องใช้ API key ที่ **คุณ** ลงทะเบียนกับ GISTDA — ห้ามใส่ใน git) มีไทล์น้ำท่วม WMS/WMTS/TMS หน้าต่าง 1/3/7/30 วัน และ flood-freq ไฟ VIIRS/burn และภัยแล้ง DRI/NDWI/SMAP 7 วัน **อ้างอิง GISTDA ทุกชั้น** ผลิตภัณฑ์น้ำท่วมจากดาวเทียมเป็นค่าที่สังเกตจากอวกาศ ไม่ใช่มาตรวัดลำน้ำ — ติดป้ายตรง ๆ ว่าการตรวจจับอัตโนมัติ **ยังไม่ได้รับการยืนยัน** (UI ของ GISTDA เองก็บอกแบบนั้น) ดึงจากเอกสารที่เปิดเผย ห้ามขูด [flood.nonarkara.org](https://flood.nonarkara.org) และห้ามใช้พร็อกซีเซสชัน `/app-api` ที่ไม่มีเอกสาร

**EN.** A named public Thai Earth-observation source: portal [`disaster.gistda.or.th`](https://disaster.gistda.or.th), Open API Swagger [`/services/open-api`](https://disaster.gistda.or.th/services/open-api), gateway [`api-gateway.gistda.or.th/api/2.0/resources`](https://api-gateway.gistda.or.th/api/2.0/resources) (**your** GISTDA-registered API key; never commit it). Flood tiles over WMS/WMTS/TMS for 1/3/7/30-day windows plus flood-freq; fire VIIRS/burn; drought DRI/NDWI/SMAP 7-day. **Attribute GISTDA on every layer.** Satellite flood products are space-based observations, not river gauges — label automated detections **not yet verified**, as GISTDA’s own UI does. Use the documented Open API. Do not scrape [flood.nonarkara.org](https://flood.nonarkara.org) and do not call undocumented `/app-api` session proxies.

## 3.2 International / free-forever sources / แหล่งข้อมูลนานาชาติ

| Source | Access | Cadence | What it gives |
|---|---|---|---|
| **Open-Meteo weather forecast** | `https://api.open-meteo.com/v1/forecast` — no key, generous free tier, supports **many coordinates in a single request**. Docs: `https://open-meteo.com/en/docs`. Credit Open-Meteo. | hourly | High-quality global weather forecast; can batch every province centroid in one call. **Modelled.** |
| **GloFAS river discharge (via Open-Meteo)** | `https://flood-api.open-meteo.com/v1/flood` — no key, wraps Copernicus GloFAS v4. Docs: `https://open-meteo.com/en/docs/flood-api`. Credit Copernicus EMS + Open-Meteo. | daily, 46-day forecast horizon | River discharge (m³/s) at 5 km global grid resolution, with ensemble forecast. **Modelled.** **Gotcha:** at 5 km resolution, a naive coordinate can land on a dry tributary instead of the main channel — scan a small grid around your target reach and keep the cell with maximum discharge |
| **RainViewer radar** | `https://api.rainviewer.com/public/weather-maps.json` + tile server | ~10 min | Global precipitation radar composite, past frames + short nowcast, renderable as animated map tiles. **Gotcha:** free tiles only carry real data to about zoom level 7; beyond that the tile is a placeholder |
| **NASA GIBS** | WMTS, no key. Docs: `https://nasa-gibs.github.io/gibs-api-docs/` · base `https://gibs.earthdata.nasa.gov/wmts/epsg3857/best/` | daily | MODIS/VIIRS true-colour satellite imagery as slippy-map tiles. **Observed imagery**, not a gauge. Credit NASA EOSDIS. |
| **NOAA ONI (Oceanic Niño Index)** | `https://www.cpc.ncep.noaa.gov/data/indices/oni.ascii.txt` — plain text, no key | monthly (revises) | The standard El Niño / La Niña classification — a genuine seasonal risk *modulator* (not a predictor) for Thailand's wet-season rainfall. **Seasonal prior.** |
| **CHIRPS climatology (optional)** | UCSB Climate Hazards Center — `https://www.chc.ucsb.edu/data/chirps` | monthly / pentad archive | Gauge-plus-satellite blended rainfall from 1981. Use for baselines, not as a live ThaiWater replacement. **Modelled/blended.** |
| **News RSS** | Any national or regional news outlet's public RSS feed, keyword-filtered | continuous | Human-language flood reporting as a cross-check against telemetry |

## 3.3 Field-tested gotchas worth inheriting / ข้อควรระวังที่คุ้มค่าจะรับไว้

**EN.**

- **A "situation level" or similar categorical field is not always
  monotonic in the direction you assume.** Verify against the raw numeric
  reading (e.g. % of bank capacity) before treating "level 5" as
  automatically worse than "level 1" — some schemes number low-water and
  high-water conditions on the same 1–5 scale, with low water at one end.
- **Government dam/reservoir feeds sometimes report a computed percentage
  as literally zero** even when storage and capacity fields are both
  populated. Compute the percentage yourself from the raw storage and
  maximum-capacity fields rather than trusting a pre-computed percentage
  field blindly.
- **A field can be `undefined`/absent rather than `null`/zero** when an
  upstream agency simply doesn't report a value for that station (e.g. no
  rated maximum capacity for a particular dam). Guard for *both* — a check
  that only excludes `null` will let `undefined` silently propagate into a
  `NaN` on-screen.
- **TLS chain issues are real.** At least one Thai government API serves an
  incomplete certificate chain that some HTTP clients (including some
  versions of Node's native fetch) reject outright, while system tools like
  `curl` resolve it fine via the OS trust store. Have a fallback fetch path.
- **Satellite flood extent is not a verified gauge.** GISTDA Disaster
  Platform products are Earth-observation layers. Automated flood
  detections should wear a badge that they are **not yet verified** until
  the agency (or your own review) says otherwise. Fetch them from the
  documented portal, Swagger, and gateway above — never from an
  undocumented `/app-api` session proxy, and never from
  [flood.nonarkara.org](https://flood.nonarkara.org).

**TH.**

- **ฟิลด์ "ระดับสถานการณ์" หรือคล้ายกันไม่ได้ไล่ทิศทางเดียวเสมอ** ตรวจสอบกับ
  ค่าตัวเลขดิบ (เช่น % ความจุตลิ่ง) ก่อนสรุปว่า "ระดับ 5" แย่กว่า "ระดับ 1"
  เสมอ — บางระบบใส่ทั้งภาวะน้ำน้อยและน้ำมากไว้ในสเกล 1–5 เดียวกัน โดยน้ำน้อย
  อยู่ปลายด้านหนึ่ง
- **ฟีดเขื่อน/อ่างเก็บน้ำภาครัฐบางครั้งรายงานเปอร์เซ็นต์ที่คำนวณแล้วเป็นศูนย์
  เปล่า ๆ** ทั้งที่ฟิลด์ปริมาณกักเก็บและความจุสูงสุดมีค่าทั้งคู่ คำนวณ
  เปอร์เซ็นต์เองจากฟิลด์ดิบ อย่าเชื่อฟิลด์เปอร์เซ็นต์สำเร็จรูปแบบไม่ตรวจสอบ
- **ฟิลด์อาจเป็น `undefined`/ไม่มีเลย แทนที่จะเป็น `null`/ศูนย์** เมื่อ
  หน่วยงานต้นทางไม่รายงานค่าของสถานีนั้นเลย (เช่น ไม่มีความจุสูงสุดที่กำหนด
  ของเขื่อนบางแห่ง) ป้องกันทั้งสองกรณี — เช็คที่กันแค่ `null` จะปล่อยให้
  `undefined` กลายเป็น `NaN` บนหน้าจอแบบเงียบ ๆ
- **ปัญหา TLS chain มีจริง** อย่างน้อยหนึ่ง API ภาครัฐไทยส่ง certificate chain
  ไม่ครบ ซึ่งบาง HTTP client (รวมถึง fetch ของ Node บางเวอร์ชัน) ปฏิเสธตรง ๆ
  ในขณะที่เครื่องมือระบบอย่าง `curl` แก้ปัญหาได้ผ่าน trust store ของ OS
  ควรมีทางดึงข้อมูลสำรอง
- **ขอบเขตน้ำท่วมจากดาวเทียมไม่ใช่มาตรวัดที่ตรวจแล้ว** ผลิตภัณฑ์แพลตฟอร์มภัยพิบัติ
  ของ GISTDA เป็นชั้นสำรวจโลก การตรวจจับน้ำท่วมอัตโนมัติควรติดป้ายว่า
  **ยังไม่ได้รับการยืนยัน** จนกว่าหน่วยงาน (หรือการตรวจของคุณเอง) จะบอกเป็นอย่างอื่น
  ดึงจากพอร์ทัล Swagger และเกตเวย์ที่ระบุไว้ — ห้ามขูดพร็อกซีเซสชัน `/app-api`
  ที่ไม่มีเอกสาร และห้ามขูด [flood.nonarkara.org](https://flood.nonarkara.org)

How these feeds become a watch score, cascade, and soil index — and a
blank-machine checklist — is in [`docs/09-compute-and-data.md`](09-compute-and-data.md).
Do not use [flood.nonarkara.org](https://flood.nonarkara.org) as an
upstream; it is illustration only.

---

[← Architecture](02-architecture.md) · [Next: The Science →](04-the-science.md)
