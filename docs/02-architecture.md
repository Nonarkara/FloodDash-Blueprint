# 2. Architecture blueprint / พิมพ์เขียวสถาปัตยกรรม

[← Why & What](01-why-and-what.md) · [Next: Data Sources →](03-data-sources.md)

---

## The shape of the system / รูปทรงของระบบ

**EN.** A working reference implementation of these ideas runs today as a
**single process, on a single machine, with zero external services required
to start.** That constraint is deliberate — it is what makes the system
deployable by a two-person team on a laptop, not just a well-funded agency.
The shape below is a *pattern*, not a prescription: build it in Python,
Go, TypeScript, whatever your team knows. The pattern is what matters.

```
                         ┌─────────────────────────────┐
   9 free public APIs ─▶ │        SCHEDULER            │  one timer per source,
   (gov + international) │  jittered · backs off on    │  never overlapping,
                         │  failure · logs every run   │  independent of the rest
                         └───────────────┬─────────────┘
                                         ▼
                         ┌─────────────────────────────┐
                         │   PER-SOURCE ADAPTERS        │  fetch → validate →
                         │   (one small module each)    │  normalize → store
                         └───────────────┬─────────────┘
                                         ▼
                         ┌─────────────────────────────┐
                         │      SINGLE-FILE DATABASE    │  append-only readings,
                         │   (station registry + time-  │  deduplicated on
                         │    series readings, WAL)     │  (source, station, metric, time)
                         └───────┬─────────────┬────────┘
                                 ▼             ▼
                    ┌─────────────────┐  ┌──────────────────┐
                    │   RISK ENGINE    │  │   EVENT BUS       │  every new reading →
                    │  (recomputed on  │  │  (in-process,     │  one event → persisted
                    │   a short cache) │  │   fans out live)  │  + streamed live
                    └────────┬─────────┘  └─────────┬────────┘
                             ▼                       ▼
                    ┌──────────────────────────────────────┐
                    │        HTTP + STREAMING API           │  JSON snapshot +
                    │   (one static-file server + routes)   │  server-sent events
                    └────────────────────┬───────────────────┘
                                         ▼
                    ┌──────────────────────────────────────┐
                    │         BILINGUAL WEB UI              │  map + ranking rail +
                    │  (plain HTML/CSS/JS — no build step   │  live tap feed +
                    │   required, but any framework works)  │  optional local-LLM chat
                    └──────────────────────────────────────┘
```

**TH.** ต้นแบบที่ใช้งานจริงตอนนี้รันเป็น **โปรเซสเดียว บนเครื่องเดียว ไม่ต้องมี
บริการภายนอกใด ๆ ก็เริ่มได้** ข้อจำกัดนี้ตั้งใจ — มันทำให้ทีมสองคนบนแล็ปท็อป
เครื่องเดียวก็ deploy ได้ ไม่ใช่แค่หน่วยงานที่มีงบเยอะ ผังข้างบนเป็น **แพทเทิร์น**
ไม่ใช่ข้อบังคับ — สร้างด้วย Python, Go, TypeScript อะไรก็ได้ที่ทีมคุณถนัด
แพทเทิร์นต่างหากที่สำคัญ

## The five modules that matter / ห้าโมดูลที่สำคัญ

### 2.1 The scheduler / ตัวจัดตารางเวลา

**EN.** One independent timer per data source, each running on its own
cadence (a 10-minute telemetry feed and a 12-hour ocean-index feed should
never share a schedule). Each source gets:

- **Jitter** — stagger the start times so nine sources don't all hit their
  upstream APIs in the same second.
- **Isolated failure** — one source erroring must never block or crash the
  others. Wrap every fetch in its own try/catch, log the failure, and retry
  on the next cycle.
- **Exponential backoff** — a source that fails repeatedly should back off
  (e.g. double the wait time up to a ceiling) rather than hammering a
  government API that might be having a bad day.
- **A visible health signal** — the UI should show, at a glance, which of
  the nine pipes are currently healthy and when each last succeeded. This
  is not a nice-to-have; it's how you catch a silently-broken upstream API
  before a province floods.

### 2.2 The adapters / อแดปเตอร์รายแหล่งข้อมูล

**EN.** One small module per data source, each with the same three-step
contract: **fetch → validate → normalize-and-store.** Validation matters
more than it looks: government APIs occasionally return stale rows (a dam
feed with dates from years ago mixed into today's response is a real,
observed failure mode), fields as strings instead of numbers, or values a
human would immediately recognise as a sentinel for "no data" (e.g. `-1`)
rather than an actual reading. A validation layer that filters `-1`,
rejects out-of-range dates, and coerces types defensively is the single
highest-leverage piece of code in the whole system, because everything
downstream trusts what it stores.

**TH.** โมดูลเล็ก ๆ หนึ่งตัวต่อหนึ่งแหล่งข้อมูล ทำสัญญาเดียวกันสามขั้น:
**ดึงข้อมูล → ตรวจสอบ → แปลงรูปแบบแล้วเก็บ** การตรวจสอบสำคัญกว่าที่คิด — API
ภาครัฐบางครั้งส่งแถวเก่าปนมา (ฟีดเขื่อนที่มีวันที่ย้อนหลังหลายปีปนกับข้อมูล
วันนี้เป็นความผิดพลาดจริงที่เจอ) ค่าที่ควรเป็นตัวเลขกลับเป็นสตริง หรือค่าที่
มนุษย์รู้ทันทีว่าหมายถึง "ไม่มีข้อมูล" (เช่น `-1`) แต่ไม่ใช่ค่าที่วัดได้จริง
ชั้นตรวจสอบที่กรอง `-1` ปฏิเสธวันที่ผิดช่วง และแปลงชนิดข้อมูลอย่างระมัดระวัง
คือโค้ดที่คุ้มค่าที่สุดในระบบทั้งหมด เพราะทุกอย่างที่อยู่ปลายน้ำเชื่อสิ่งที่มันเก็บ

### 2.3 The database / ฐานข้อมูล

**EN.** Two tables carry almost the entire system: a **station registry**
(one row per physical sensor/gauge/reach, upserted as new metadata arrives)
and an **append-only readings table** (source, station key, metric name,
value, observation time, fetch time). The single most important design
decision in the whole schema is a **uniqueness constraint on
`(source, station_key, metric, observation_time)`** with an
insert-or-ignore write policy. This makes every fetch cycle **idempotent**:
polling the same upstream endpoint twice never creates duplicate rows,
which means your scheduler can be sloppy about retries without corrupting
history. Keep a **latest-value** side table (or equivalent cache) for O(1)
"what's the current reading" lookups — recomputing the max over a growing
time-series table on every page load does not scale.

For long-term storage: raw readings are useful in full resolution for a
finite window (60–90 days is generous), after which they can be rolled up
into permanent hourly min/max/average aggregates. This lets a modest laptop
store *years* of pattern history in a few hundred megabytes instead of
gigabytes.

**TH.** สองตารางแบกระบบเกือบทั้งหมด: **ทะเบียนสถานี** (หนึ่งแถวต่อเซนเซอร์/
สถานีจริงหนึ่งตัว อัปเดตเมื่อมีเมทาดาทาใหม่) และ **ตารางค่าที่วัดได้แบบเพิ่ม
อย่างเดียว** (แหล่งข้อมูล รหัสสถานี ชื่อตัวชี้วัด ค่า เวลาที่วัด เวลาที่ดึง)
การตัดสินใจออกแบบที่สำคัญที่สุดในสคีมาทั้งหมดคือ **ข้อจำกัดความไม่ซ้ำบน
`(แหล่งข้อมูล, รหัสสถานี, ตัวชี้วัด, เวลาที่วัด)`** พร้อมนโยบายเขียนแบบ
insert-or-ignore ทำให้ทุกรอบการดึงข้อมูล **idempotent** — ดึงจากปลายทางเดิม
ซ้ำสองครั้งไม่มีวันสร้างแถวซ้ำ แปลว่า scheduler ลองใหม่มั่ว ๆ ได้โดยไม่ทำ
ประวัติเสีย เก็บตาราง **ค่าล่าสุด** แยกไว้ (หรือแคชแบบเดียวกัน) เพื่อดูค่า
ปัจจุบันแบบ O(1) — การคำนวณค่าสูงสุดจากตารางอนุกรมเวลาที่โตขึ้นทุกครั้งที่โหลด
หน้าเว็บไม่สเกล

สำหรับการเก็บระยะยาว: ข้อมูลดิบมีประโยชน์แบบละเอียดในช่วงจำกัด (60–90 วันก็เกิน
พอ) หลังจากนั้นสรุปเป็น hourly min/max/average ถาวรได้ ทำให้แล็ปท็อปธรรมดา
เก็บประวัติรูปแบบได้ **หลายปี** ในไม่กี่ร้อยเมกะไบต์แทนที่จะเป็นกิกะไบต์

### 2.4 The event bus / บัสเหตุการณ์

**EN.** Every new reading, alert, or pipeline-status change should flow
through **one** in-process event bus. Two consumers subscribe to it: a
persistence layer (write every event to a log table — this becomes an
audit trail you can replay after an incident) and a live-streaming layer
(fan the same events out to connected browser clients in real time, via
Server-Sent Events or WebSockets). This is what produces the "running tap"
UX — a visible, scrolling feed of every new datum as it arrives, which
does double duty as both a delightful live-ness signal for users and a
debugging tool for operators ("did the dam pipeline actually run in the
last hour? scroll the tap and see").

Design the stream with **reconnection in mind from day one**: browsers
sleep, networks flap, and a 24/7 system needs its live feed to resume
cleanly with a small backlog replay rather than silently going stale.

**TH.** ค่าที่วัดได้ใหม่ การแจ้งเตือน หรือการเปลี่ยนสถานะท่อข้อมูลทุกอย่างควร
ไหลผ่าน **บัสเหตุการณ์ในโปรเซสเดียว** ตัวเดียว มีผู้รับสองฝั่ง: ชั้นบันทึก
(เขียนทุกเหตุการณ์ลงตารางล็อก — กลายเป็นร่องรอยตรวจสอบที่ย้อนดูได้หลัง
เหตุการณ์) และชั้นสตรีมสด (กระจายเหตุการณ์เดียวกันไปยังเบราว์เซอร์ที่เชื่อมต่อ
อยู่แบบเรียลไทม์ ผ่าน Server-Sent Events หรือ WebSocket) นี่คือที่มาของ UX
"ท่อข้อมูลไหลสด" — ฟีดที่เลื่อนแสดงค่าใหม่ทุกค่าที่มาถึง ทำหน้าที่สองอย่าง
พร้อมกัน: เป็นสัญญาณความสดที่ผู้ใช้ชอบ และเป็นเครื่องมือดีบั๊กของผู้ดูแลระบบ
("ท่อเขื่อนรันจริงในชั่วโมงที่แล้วไหม เลื่อนดูท่อข้อมูลก็รู้")

ออกแบบสตรีมโดยคิดเรื่อง **การเชื่อมต่อใหม่ตั้งแต่วันแรก** — เบราว์เซอร์หลับ
เครือข่ายกระตุก ระบบที่รัน 24 ชั่วโมงต้องให้ฟีดสดกลับมาทำงานได้อย่างราบรื่น
พร้อม replay ย้อนหลังเล็กน้อย ไม่ใช่เงียบไปเฉย ๆ

### 2.5 The presentation layer / ชั้นแสดงผล

**EN.** Keep the frontend as boring and dependency-free as your team's
comfort level allows. A vanilla-JS, no-build-step frontend served straight
from the same process as the API means there is nothing to compile, nothing
to break between Node versions, and nothing to debug except your own code.
A framework is a perfectly fine choice too — the architectural requirement
that actually matters is **fixed-viewport, no page scroll**: on a control-
room screen or a phone in the field, the map must never scroll off-screen
just because a side panel got a long list. Panels scroll internally; the
map is the one constant.

An **optional, gracefully-degrading local-LLM chat layer** is worth adding
last, once the core is solid: inject only real numbers pulled fresh from
the database into the model's context (never let the model estimate a
number itself), and design the UI so that if the local model is offline,
the chat panel falls back to a plain structured summary of the same data —
never a blank or broken feature.

**TH.** ทำหน้าบ้านให้ธรรมดาและไม่พึ่งพา dependency เท่าที่ทีมสบายใจ หน้าบ้าน
แบบ vanilla JS ไม่มีขั้นตอน build เสิร์ฟจากโปรเซสเดียวกับ API แปลว่าไม่มีอะไร
ต้อง compile ไม่มีอะไรพังระหว่างเวอร์ชัน Node และไม่มีอะไรต้องดีบั๊กนอกจากโค้ด
ของตัวเอง จะใช้เฟรมเวิร์กก็ได้เช่นกัน — ข้อกำหนดสถาปัตยกรรมที่สำคัญจริง ๆ คือ
**วิวพอร์ตคงที่ ไม่มีการเลื่อนหน้า** บนจอห้องควบคุมหรือมือถือภาคสนาม แผนที่ต้อง
ไม่เลื่อนหายไปแค่เพราะแผงข้างมีรายการยาว แผงเลื่อนภายในตัวเอง แผนที่คือสิ่งเดียว
ที่คงที่

**ชั้นแชท LLM local เป็นทางเลือก** ควรเพิ่มทีหลังสุด เมื่อแกนหลักมั่นคงแล้ว —
ใส่แค่ตัวเลขจริงที่ดึงจากฐานข้อมูลสด ๆ เข้าไปในบริบทของโมเดล (อย่าให้โมเดล
ประมาณตัวเลขเอง) และออกแบบ UI ให้ถ้าโมเดล local ออฟไลน์ แผงแชทถอยไปแสดงสรุป
ข้อมูลแบบมีโครงสร้างชุดเดียวกัน — ไม่ใช่ฟีเจอร์ว่างเปล่าหรือพัง

---

[← Why & What](01-why-and-what.md) · [Next: Data Sources →](03-data-sources.md)
