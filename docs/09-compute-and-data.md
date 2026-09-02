# 9. Compute kit and data / ชุดคำนวณและข้อมูล

[← Lessons from production](08-lessons-from-production.md) · [Back to README](../README.md)

---

**EN.** Everything a builder must stand up to compute their own watch —
machine, store, cadence, public feeds with attribution, how those feeds
become risk layers, a minimal schema, an optional RAG path that does not
use anyone else's corpus, and a blank-machine checklist. **No secrets.
No proprietary dumps. No scraping [flood.nonarkara.org](https://flood.nonarkara.org).**

**TH.** ทุกอย่างที่ผู้สร้างต้องตั้งขึ้นเพื่อคำนวณระบบเฝ้าระวังของตนเอง —
เครื่อง ที่เก็บ ความถี่ ฟีดสาธารณะพร้อมการอ้างอิง วิธีเปลี่ยนฟีดเป็นชั้นความเสี่ยง
สคีมาขั้นต่ำ เส้นทาง RAG ทางเลือกที่ไม่ใช้คลังของคนอื่น และรายการตรวจบนเครื่องเปล่า
**ไม่มีความลับ ไม่มีดัมพ์กรรมสิทธิ์ ไม่ขูด [flood.nonarkara.org](https://flood.nonarkara.org)**

---

## 9.1 What to stand up / สิ่งที่ต้องตั้ง

| Piece / ส่วน | Starting choice / ทางเริ่ม | When to change / เมื่อไหร่จึงเปลี่ยน |
|---|---|---|
| Machine | One Mac that does not sleep, **or** a small always-on VPS (1–2 vCPU, 2–4 GB RAM) | Graduate when disk I/O or concurrent writers hurt; not before |
| Process supervisor | `launchd` (macOS) / `systemd` (Linux) | Never "a terminal window" |
| Public URL | Cloudflare Tunnel → `localhost`, **or** nginx/Caddy on the VPS | Same catch-all `/api/*` idea either way |
| Static UI | Cloudflare Pages (or any CDN) | Keep it decoupled from ingest |
| Store | **SQLite, WAL mode**, one file | TimescaleDB/Postgres when one file's writers or ops team demand it — [deep dive 6](deep-dive/06-open-source-stack.md) |
| Language | Whatever your team already ships | The blueprint is the pattern, not a stack |

**EN.** SQLite in WAL mode is enough past a gigabyte of readings on a
laptop. The failure mode is **disk**, not the database engine. Budget
tens of megabytes per day for a national-scale ingest of the catalogs
below, plus logs. That is an order-of-magnitude planning number, not a
promise. Measure yours after a week.

**TH.** SQLite โหมด WAL พอสำหรับค่าที่วัดได้เกินหนึ่งกิกะไบต์บนแล็ปท็อป
โหมดล้มเหลวคือ **ดิสก์** ไม่ใช่เอนจินฐานข้อมูล กะวันละหลายสิบเมกะไบต์สำหรับ
ingest ระดับประเทศจากแคตตาล็อกด้านล่าง บวกล็อก นั่นคือตัวเลขวางแผนลำดับขนาด
ไม่ใช่คำสัญญา วัดของตัวเองหลังหนึ่งสัปดาห์

---

## 9.2 Ingest cadence / ความถี่การดึง

Match the upstream, then add jitter. Do not poll faster than the agency
publishes — you will not get fresher truth, only a ban.

| Source family | Typical cadence | Suggested poll |
|---|---|---|
| HII ThaiWater water / rain | 10–15 min | 10–15 min + jitter |
| HII dam feed | hourly–daily | hourly; **filter observation date** |
| RID reservoirs | daily | 1–6 h (idempotent upsert) |
| DWR early-warning stations | ~15 min | 15 min + jitter |
| Air4Thai | hourly | hourly |
| TMD obs / NWP | hourly / 3-hourly | match product; needs free `uid`/`ukey` |
| Open-Meteo forecast | hourly model | 1–3 h; batch province centroids |
| GloFAS via Open-Meteo | daily | 3–12 h |
| RainViewer radar | ~10 min | 10 min (tiles; cap zoom) |
| NASA GIBS | daily | tiles in the browser, not your DB |
| NOAA ONI | monthly | weekly is plenty |
| News RSS | continuous | 15–30 min, keyword filter |
| GISTDA Disaster Platform tiles | flood 1/3/7/30-day + flood-freq; fire; drought 7-day | daily; map layer, not a gauge; badge unverified auto-flood |

Health endpoint: JSON with `ok`, clock time, and **last success per
source**. Cadence ≠ latency — show data age in the UI.

---

## 9.3 Data catalog — public URLs and attribution / แคตตาล็อกข้อมูล — URL สาธารณะและการอ้างอิง

**EN.** Full field notes live in [`docs/03-data-sources.md`](03-data-sources.md)
and [`docs/deep-dive/02-data-sources-deep-dive.md`](deep-dive/02-data-sources-deep-dive.md).
This table is the **compute kit**: what to fetch, who to credit, measured
vs modelled. Endpoints below are **agency and open-data URLs**, not the
illustration site. They were verified as public at the time the main
catalog was written; agencies move paths — if a URL 404s, start from the
agency portal, not from someone else's dashboard.

**TH.** รายละเอียดระดับฟิลด์อยู่ใน [`docs/03-data-sources.md`](03-data-sources.md)
และ [`docs/deep-dive/02-data-sources-deep-dive.md`](deep-dive/02-data-sources-deep-dive.md)
ตารางนี้คือ **ชุดคำนวณ**: ดึงอะไร อ้างใคร วัดหรือจำลอง จุดเชื่อมด้านล่างเป็น
**URL ของหน่วยงานและข้อมูลเปิด** ไม่ใช่เว็บภาพประกอบ หาก 404 ให้เริ่มจากพอร์ทัลหน่วยงาน

### Thai government / ภาครัฐไทย

| Source | Attribution | Public URL | Kind |
|---|---|---|---|
| **HII ThaiWater v3 — water level** | Hydro-Informatics Institute (สสน.) | `https://api-v3.thaiwater.net/api/v1/thaiwater30/public/waterlevel` | **Measured** |
| **HII ThaiWater — 24h rain** | HII / multi-agency gauges | `https://api-v3.thaiwater.net/api/v1/thaiwater30/public/rain_24h` | **Measured** |
| **HII ThaiWater — dams** | HII / EGAT / RID | `https://api-v3.thaiwater.net/api/v1/thaiwater30/analyst/dam` | **Measured** (filter stale dates) |
| **RID reservoirs** | Royal Irrigation Department (กรมชลประทาน) | `https://app.rid.go.th/reservoir/api/reservoir/public` · dated: `…/public/{YYYY-MM-DD}` | **Measured** |
| **DWR early warning** | Department of Water Resources (กรมทรัพยากรน้ำ) | portal `https://ews.dwr.go.th` · stations `https://ews.dwr.go.th/ews/web-service/stn` | **Measured** (incl. soil / flash-flood status) |
| **Air4Thai** | Pollution Control Department (กรมควบคุมมลพิษ) | `https://air4thai.pcd.go.th/services/getNewAQI_JSON.php` (HTTPS; filter `-1`) | **Measured** |
| **TMD observations & NWP** | Thai Meteorological Department (กรมอุตุฯ) | portal `https://data.tmd.go.th/api/` · register `https://data.tmd.go.th/api/registerPre.php` | Obs = **measured**; NWP = **modelled** |
| **GISTDA flood map / Sphere** | GISTDA | flood map `https://flood.gistda.or.th` · Sphere `https://sphere.gistda.or.th` | Related portals; see Disaster Platform below for documented EO APIs |
| **GISTDA Disaster Platform** | **GISTDA** (สำนักงานพัฒนาเทคโนโลยีอวกาศและภูมิสารสนเทศ) | portal `https://disaster.gistda.or.th` · Swagger `https://disaster.gistda.or.th/services/open-api` · gateway `https://api-gateway.gistda.or.th/api/2.0/resources` (**your** key) | Flood WMS/WMTS/TMS 1/3/7/30-day + flood-freq; fire VIIRS/burn; drought DRI/NDWI/SMAP 7-day. **Satellite-derived** — label unverified auto-flood honestly |

ThaiWater public/analyst routes as catalogued in [§3.1](03-data-sources.md)
do not require a key. TMD and GISTDA Disaster Platform / Sphere services
that need a key use **your** free registration — put those ids in env,
never in git.

**GISTDA Disaster Platform.** Attribution: **GISTDA**. Portal
`https://disaster.gistda.or.th`, Swagger
`https://disaster.gistda.or.th/services/open-api`, gateway
`https://api-gateway.gistda.or.th/api/2.0/resources`. Flood 1/3/7/30-day
and flood-freq tiles (WMS/WMTS/TMS), fire VIIRS/burn, drought
DRI/NDWI/SMAP 7-day. Satellite flood is **not** a verified gauge; badge
automated extent as not-yet-verified. Do not scrape
[flood.nonarkara.org](https://flood.nonarkara.org) or undocumented
`/app-api` session proxies.

**แพลตฟอร์มภัยพิบัติ GISTDA.** อ้างอิง **GISTDA** ทุกชั้น พอร์ทัลและ Open API
ตามตารางด้านบน น้ำท่วมจากดาวเทียมไม่ใช่มาตรวัดที่ตรวจแล้ว — ติดป้ายว่า
ยังไม่ยืนยันตามที่ UI ของหน่วยงานบอก ห้ามขูดเว็บภาพประกอบหรือพร็อกซีเซสชัน
ที่ไม่มีเอกสาร

### International / นานาชาติ

| Source | Attribution | Public URL | Kind |
|---|---|---|---|
| **Open-Meteo forecast** | Open-Meteo (non-commercial free tier; credit them) | `https://api.open-meteo.com/v1/forecast` · docs `https://open-meteo.com/en/docs` | **Modelled** |
| **GloFAS discharge** | Copernicus EMS GloFAS v4, wrapped by Open-Meteo | `https://flood-api.open-meteo.com/v1/flood` · docs `https://open-meteo.com/en/docs/flood-api` | **Modelled** |
| **RainViewer** | RainViewer | `https://api.rainviewer.com/public/weather-maps.json` + their tile server | Composite / **nowcast** — **modelled** past a nowcast step |
| **NASA GIBS** | NASA EOSDIS | WMTS docs `https://nasa-gibs.github.io/gibs-api-docs/` · `https://gibs.earthdata.nasa.gov/wmts/epsg3857/best/...` | Imagery (**observed** satellite, not a gauge) |
| **NOAA ONI** | NOAA Climate Prediction Center | `https://www.cpc.ncep.noaa.gov/data/indices/oni.ascii.txt` | **Seasonal prior**, not a Tuesday forecast |
| **CHIRPS rainfall climatology** | UCSB CHC / USGS (optional long baseline) | `https://www.chc.ucsb.edu/data/chirps` | **Modelled/blended** archive — not live ThaiWater |
| **News RSS** | Each publisher | e.g. keyword-filtered national RSS — see your outlet's published feed | Human report; never a substitute for a gauge |

**Do not fetch:** `https://flood.nonarkara.org` or any `api-*.nonarkara.org`
you might guess. Those are not this kit.

---

## 9.4 From feeds to risk layers / จากฟีดสู่ชั้นความเสี่ยง

Formulas in full: [`docs/04-the-science.md`](04-the-science.md). This is
the wiring diagram.

```
MEASURED                          DERIVED FROM MEASURED              MODELLED / PRIOR
─────────                         ─────────────────────              ────────────────
HII water, rain, dams             watch score  (§4.1)                Open-Meteo rain
RID storage, DWR stations         rise rate    (§4.4)                GloFAS discharge
Air4Thai, TMD observations        soil API     (§4.3) from *your*    TMD NWP
                                  stored rain history                RainViewer nowcast
                                  cascade lag  (§4.2) using          NOAA ONI (seasonal)
                                  GloFAS as modelled flow
                                  + your graph of reaches
GISTDA Disaster Platform tiles    satellite flood/fire/drought       badge: auto-flood not yet verified
(WMS/WMTS/TMS — credit GISTDA)    (space-based, not a river gauge)
```

**Watch score (heuristic, not a forecast):**

```
score = w1·water + w2·rain + w3·forecast + w4·soil_wetness + w5·rise_rate
```

Starting weights that can be said in one sentence: water 40–50%, rain
25–30%, forecast 15–20%, refinements 10–20%. **Publish the weights in
the product.** Map `water` from the worst live station in the region
(situation level or % bank — and remember HII's 1–5 scale is not
monotonic in the direction you assume; check [§3.3](03-data-sources.md)).
Map `rain` from worst 24h gauge. Map `forecast` from Open-Meteo or TMD
NWP for the same region, **labelled modelled**.

**Soil memory** needs *your* rain history:

```
API_t = k · API_(t-1) + P_t     (k ≈ 0.92 default)
```

You cannot compute this on day one from the live rain endpoint alone.
That is one reason [historical SQLite is irreplaceable](08-lessons-from-production.md#84-historical-sqlite-cannot-be-rebuilt-from-apis--sqlite-ประวัติศาสตร์สร้างใหม่จาก-api-ไม่ได้).

**Cascade:** a small directed graph of reaches (downstream link + transit
lag in days) × GloFAS discharge at each reach. Scan a small coordinate
grid and keep max discharge so you do not land on a dry tributary.
Thailand worked example: Ping + Nan → Nakhon Sawan → Chai Nat →
Ayutthaya → Bangkok, on the order of **five days** under normal flow
([§4.2](04-the-science.md)).

**Every layer wears a badge.** Suggested enum for builders (names are
yours; the honesty is not optional):

| Badge | Means |
|---|---|
| `measured` | Gauge/agency reading in this fetch |
| `derived` | Score/API/rise computed from stored measured rows |
| `modelled` | NWP, GloFAS, nowcast, SAR extent |
| `seasonal` | ONI / climatology |
| `unavailable` | Gap — show the timestamp, never a fake number |

---

## 9.5 Minimal schema concepts / แนวสคีมาขั้นต่ำ

**EN.** Concepts, not a proprietary dump. Two tables carry almost the
system ([§2.3](02-architecture.md)).

**TH.** แนวคิด ไม่ใช่ดัมพ์กรรมสิทธิ์ สองตารางแบกระบบเกือบทั้งหมด

```
stations (
  source, station_key, name_th, name_en,
  lat, lon, region_key, meta_json,
  PRIMARY KEY (source, station_key)
)

readings (
  source, station_key, metric, value,
  observed_at, fetched_at, quality,
  UNIQUE (source, station_key, metric, observed_at)
)

latest (          -- cache; rebuildable from readings
  source, station_key, metric, value, observed_at
)

pipeline_runs (   -- health; not rebuildable from agency APIs
  source, started_at, finished_at, ok, rows_upserted, error
)

events (          -- tap / alerts; not rebuildable from agency APIs
  ts, kind, payload_json
)
```

`insert-or-ignore` on the unique readings key makes retries idempotent.
Roll up raw rows after 60–90 days into hourly min/max/avg if the file
must stay laptop-sized. **`pipeline_runs` and `events` are why a fresh
fetch is not a restore.**

Optional, **your documents only**:

```
knowledge_chunks (
  id, title, lang, text, source_path, updated_at
)
knowledge_vectors (
  chunk_id, provider, dim, embedding_blob
)
```

Do not populate those from a private twin. If you skip vectors, a
`LIKE` search over `knowledge_chunks` of **your** methodology page is
enough.

---

## 9.6 Optional RAG path (no leaked corpus) / เส้นทาง RAG ทางเลือก (ไม่รั่วคลัง)

1. Write your own bilingual methodology (this blueprint, CC BY 4.0, is
   legal to adapt **with attribution**).
2. Chunk *those* files into `knowledge_chunks`.
3. If you embed: `EMBEDDING_PROVIDER=` + `EMBEDDING_API_KEY=` in env,
   or a local model (`OLLAMA_HOST=`), or Transformers.js in the browser.
4. At answer time: retrieve chunks, then **inject live rows from
   `latest`**. The model may paraphrase your docs. It may not invent a
   centimetre of water.
5. If the provider is down, fall back to the structured summary of
   `latest` — never a blank chat that looks authoritative.

---

## 9.7 Named env vars — values never in git / ชื่อตัวแปรสภาพแวดล้อม — ค่าไม่อยู่ใน git

**EN.** Names for *your* deployment. Leave the right-hand side empty in
any file that is committed. Fill values only on the machine.

**TH.** ชื่อสำหรับการติดตั้ง *ของคุณ* ฝั่งขวาว่างในทุกไฟล์ที่ commit
ใส่ค่าบนเครื่องเท่านั้น

```env
# --- store (paths, not secrets, but machine-specific — still keep out of git) ---
DATA_DIR=
DATABASE_PATH=
BACKUP_DIR=

# --- public origin you own (not flood.nonarkara.org) ---
PUBLIC_ORIGIN=
API_ORIGIN=

# --- tunnel / process ---
TUNNEL_CONFIG_PATH=
LISTEN_PORT=

# --- agencies that require YOUR free registration ---
TMD_UID=
TMD_UKEY=
GISTDA_API_KEY=

# --- optional chat / embeddings (your keys, your corpus) ---
OLLAMA_HOST=
EMBEDDING_PROVIDER=
EMBEDDING_API_KEY=

# --- never commit: Cloudflare tokens, tunnel JSON, account ids ---
# CLOUDFLARE_API_TOKEN=
# CLOUDFLARE_ACCOUNT_ID=
```

### What cannot come from git / สิ่งที่มาจาก git ไม่ได้

- Any value for the names above
- `~/.cloudflared/` credentials (`cert.pem`, tunnel `*.json`)
- Cloudflare account ids and API tokens
- Historical `.sqlite` / WAL/SHM companions
- Embedding blobs and knowledge corpora you did not write
- PII, personal disk paths, other people's `.env`

Git may hold: this blueprint, your *code*, an env **name list**,
`wrangler.toml` **structure**, and a Pages Function that reads
`API_ORIGIN` at runtime.

---

## 9.8 Setup checklist / รายการติดตั้งบนเครื่องเปล่า

Blank Mac or VPS. Order matters. Illustration site is not a step.

### A. Machine

- [ ] Always-on: sleep disabled (Mac) or a VPS with restart-on-reboot
- [ ] Disk watchdog on the **data** volume; alert before 85%
- [ ] Supervisor installed (`launchd` / `systemd`)

### B. Store

- [ ] Create `DATA_DIR` via env, not a hardcoded home-folder story
- [ ] Empty SQLite with `stations` / `readings` unique key
- [ ] Separate `BACKUP_DIR` on another volume if you can
- [ ] Prove restore once before you trust ingest

### C. One pipeline (Phase 0)

- [ ] Fetch **one** public URL from §9.3 (HII water level is the richest start)
- [ ] Validate types, dates, sentinels (`-1`)
- [ ] Two poll cycles, **zero duplicate rows**
- [ ] Do not build a map yet

### D. Remaining pipes

- [ ] One adapter per source, own cadence, own backoff
- [ ] Health JSON: last success per source
- [ ] Overnight unattended run

### E. Risk layers

- [ ] Score from §4.1, weights published in the UI
- [ ] Badges: measured / derived / modelled / unavailable
- [ ] Cascade graph + GloFAS only after Phase 2 is honest
- [ ] Soil API only after you have *your* rain history

### F. Public surface

- [ ] Static UI on a CDN
- [ ] Catch-all `/api/*` proxy; **one** `API_ORIGIN`
- [ ] Grep for leftover `api2-…` hostnames
- [ ] Each tunnel/proxy has its **own** config file
- [ ] Official-warning disclaimer on every score screen

### G. Operate

- [ ] Four jobs: server, tunnel/proxy, watchdog, nightly backup
- [ ] Soak one week; memory stable; live feed survives reboot
- [ ] **Never** point adapters at [flood.nonarkara.org](https://flood.nonarkara.org)

---

## 9.9 Illustration, not a source / ภาพประกอบ ไม่ใช่ซอร์ส

**EN.** [https://flood.nonarkara.org](https://flood.nonarkara.org) shows
what a reconstruction *can look like*. It is not a data source, not a
CDN for this repo, and not the private FloodDash tree. Reconstruct from
`docs/`. Compute from the public URLs in §9.3.

**TH.** [https://flood.nonarkara.org](https://flood.nonarkara.org) แสดงว่า
การสร้างใหม่ *หน้าตาเป็นอย่างไรได้* ไม่ใช่แหล่งข้อมูล ไม่ใช่ CDN ของ repo นี้
และไม่ใช่ต้นไม้ FloodDash ส่วนตัว สร้างจาก `docs/` คำนวณจาก URL สาธารณะในข้อ 9.3

---

[← Lessons from production](08-lessons-from-production.md) · [Back to README](../README.md)
