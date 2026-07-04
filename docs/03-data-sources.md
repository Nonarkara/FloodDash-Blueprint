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
| **ThaiWater v3 API** | HII — Hydro-Informatics Institute (สสน.) | `api-v3.thaiwater.net/api/v1/thaiwater30/` — no key on `/public/` and `/analyst/` endpoints | 10–15 min | Water level (m MSL), % bank capacity, HII situation level (1 critically-low … 5 overflowing), across hundreds of telemetry stations nationwide |
| **ThaiWater rain** | Multi-agency, aggregated via HII | same base, `/public/rain_24h` | 10–15 min | 1-hour and 24-hour rainfall accumulation, thousands of gauges |
| **ThaiWater dam feed** | HII / EGAT / RID | same base, `/analyst/dam` | hourly–daily | Major dam storage (MCM), inflow, release. **Gotcha:** the feed mixes recent and years-old stale rows — filter by observation date before trusting a row |
| **RID reservoir API** | Royal Irrigation Department (กรมชลประทาน) | `app.rid.go.th/reservoir/api/reservoir/public` — no key; date-specific variant at `/public/{YYYY-MM-DD}` | daily | Medium reservoir storage %, volume (MCM), for several hundred reservoirs nationwide |
| **Air4Thai** | Pollution Control Department (กรมควบคุมมลพิษ) | `air4thai.pcd.go.th/services/getNewAQI_JSON.php` — **must use HTTPS**; some HTTP clients reject its TLS chain, curl usually works | hourly | AQI and PM2.5 at ground stations nationwide. **Gotcha:** missing values are sent as `-1`, not `null` — filter them |
| **TMD observations & NWP** | Thai Meteorological Department | `data.tmd.go.th/api/` (free registration for a `uid`/`ukey` pair); higher-resolution NWP forecast API separately registered | hourly (obs), 3-hourly (3 km NWP forecast) | Ground weather observations and short-range numerical weather prediction — the highest-quality public rain forecast for Thailand |
| **GISTDA flood/satellite** | Geo-Informatics and Space Technology Development Agency | WMS/WMTS/TMS map services + GeoJSON feature endpoints (API key embedded in their own public dashboard) | 1–7 day aggregation windows | SAR-derived flood-extent layers that see through monsoon cloud cover |

## 3.2 International / free-forever sources / แหล่งข้อมูลนานาชาติ

| Source | Access | Cadence | What it gives |
|---|---|---|---|
| **Open-Meteo weather forecast** | `api.open-meteo.com/v1/forecast` — no key, generous free tier, supports **many coordinates in a single request** | hourly | High-quality global weather forecast; can batch every province centroid in one call |
| **GloFAS river discharge (via Open-Meteo)** | `flood-api.open-meteo.com/v1/flood` — no key, wraps Copernicus GloFAS v4 | daily, 46-day forecast horizon | River discharge (m³/s) at 5 km global grid resolution, with ensemble forecast. **Gotcha:** at 5 km resolution, a naive coordinate can land on a dry tributary instead of the main channel — scan a small grid around your target reach and keep the cell with maximum discharge |
| **RainViewer radar** | `api.rainviewer.com/public/weather-maps.json` + tile server | ~10 min | Global precipitation radar composite, past frames + short nowcast, renderable as animated map tiles. **Gotcha:** free tiles only carry real data to about zoom level 7; beyond that the tile is a placeholder |
| **NASA GIBS** | WMTS tile endpoints, no key | daily | MODIS/VIIRS true-colour satellite imagery as slippy-map tiles |
| **NOAA ONI (Oceanic Niño Index)** | `www.cpc.ncep.noaa.gov/data/indices/oni.ascii.txt` — plain text, no key | monthly (revises) | The standard El Niño / La Niña classification — a genuine seasonal risk *modulator* (not a predictor) for Thailand's wet-season rainfall |
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

---

[← Architecture](02-architecture.md) · [Next: The Science →](04-the-science.md)
