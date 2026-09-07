# 8. Lessons from production / บทเรียนจากระบบที่รันจริง

[← Honest Limitations](07-honest-limitations.md) · [Next: Compute & data →](09-compute-and-data.md)

---

**EN.** These are **advisory notes for builders**, distilled from operating a
one-machine flood watch in weather — not a dump of anyone's private ops,
and not a substitute for reading [the architecture](02-architecture.md)
and [the honest limitations](07-honest-limitations.md) first.

**Fork the method, not the secrets.** Nothing below is a credential, a
Cloudflare account id, a tunnel token, a `.env` value, a historical
database, or a path on someone else's disk. [flood.nonarkara.org](https://flood.nonarkara.org)
is an illustration of the method in weather. Do not scrape it for source.

**TH.** นี่คือ **คำแนะนำสำหรับผู้สร้าง** สกัดจากการดูแลระบบเฝ้าระวังน้ำท่วม
บนเครื่องเดียวในอากาศจริง — ไม่ใช่การเทข้อมูลปฏิบัติการส่วนตัวของใคร และไม่ใช่
สิ่งทดแทนการอ่าน[สถาปัตยกรรม](02-architecture.md)กับ[ข้อจำกัดอย่างซื่อสัตย์](07-honest-limitations.md)
ก่อน

**สืบทอดวิธี ไม่ใช่ความลับ** ไม่มีอะไรด้านล่างที่เป็น credential, Cloudflare
account id, token ของอุโมงค์, ค่าใน `.env`, ฐานข้อมูลประวัติ หรือ path บนดิสก์
ของคนอื่น [flood.nonarkara.org](https://flood.nonarkara.org) เป็นภาพประกอบของวิธี
ในอากาศจริง อย่าขูดเพื่อเอาซอร์ส

---

## 8.1 The shape that survives a sleeping Mac / รูปทรงที่รอดเมื่อแม็กหลับ

**EN.** Put the **static bilingual UI on a CDN** (Cloudflare Pages or
equivalent). Put the **ingest process on one Mac or VPS that you control**.
Join them with a **catch-all proxy** (`/api/*`, no hand-maintained route
list) talking to a **named tunnel** (or a reverse proxy on a VPS) to
`localhost:PORT`.

If the machine sleeps, the *site still loads*. Only live numbers go
stale — and the UI must say how stale. That split is why a flood dashboard
does not have to die when a laptop lid closes. A dashboard that goes
blank during a flood has negative value; a dashboard that stays up and
admits "no ingest since 14:32" still has value.

**TH.** วาง **UI สองภาษาแบบไฟล์นิ่งบน CDN** (Cloudflare Pages หรือเทียบเท่า)
วาง **โปรเซสดึงข้อมูลบน Mac หรือ VPS เครื่องเดียวที่คุณควบคุม** เชื่อมด้วย
**พร็อกซีแบบจับทั้งหมด** (`/api/*` ไม่มีรายการเส้นทางที่ต้องไล่แก้เอง) ไปยัง
**อุโมงค์ที่มีชื่อ** (หรือ reverse proxy บน VPS) เข้า `localhost:PORT`

ถ้าเครื่องหลับ *เว็บยังโหลดได้* มีแค่ตัวเลขสดที่เก่า — และ UI ต้องบอกว่าเก่า
แค่ไหน การแยกชั้นนี้ทำให้แดชบอร์ดน้ำท่วมไม่ต้องตายเมื่อฝาแล็ปท็อปปิด
แดชบอร์ดที่ว่างเปล่าระหว่างน้ำท่วมมีค่าติดลบ แดชบอร์ดที่ยังขึ้นแล้วสารภาพว่า
"ไม่มี ingest ตั้งแต่ 14:32" ยังมีค่า

```
browser  →  CDN static UI  →  Pages Function /api/* catch-all
                                      ↓
                              named tunnel / reverse proxy
                                      ↓
                         localhost:PORT  →  SQLite (WAL)
```

Four jobs beat one clever job: **server**, **tunnel (or proxy)**,
**watchdog**, **nightly backup**. One process that tries to be all four
stops restarting, stops backing up, and stops telling you, on the same
day you needed all three.

---

## 8.2 Tunnel hostname traps (`api-…` vs `api2-…`) / กับดักชื่อโฮสต์ของอุโมงค์

**EN.** Named tunnels, DNS CNAMEs, Pages Function upstreams, and env vars
must all agree on **one** API hostname. A rebuild, a "temporary" second
tunnel, or a Cloudflare UI default will happily mint a sibling name —

`api-yourapp` vs `api2-yourapp`

— and everything will look healthy. The tunnel process is up. DNS is
green. The Pages Function returns 200 from *a* host. Meanwhile the
browser is calling the hostname you stopped using last Tuesday.

The production-shaped trap is exactly that pair: **`api-flood` vs
`api2-flood`**. The `2` is not a version. It is a leftover. Grep the
whole tree — Pages Function, `wrangler.toml` `[vars]`, tunnel ingress,
DNS records, health-check URL, docs you will follow at 2 a.m. — for every
API hostname before you call it live. Put the canonical name in env
(`API_ORIGIN=`, empty in git) and never hardcode two of them.

Poison the silent fallback: `cloudflared tunnel run <name>` with no
`--config` falls back to a global file and **two tunnels will overwrite
each other's ingress with no error**. Give every tunnel its own config
file, referenced by an explicit `--config` flag. Make the global file
inert (`http_status:404` only).

**TH.** อุโมงค์ที่มีชื่อ, DNS CNAME, upstream ของ Pages Function และตัวแปร
สภาพแวดล้อม ต้องชี้ **โฮสต์ API เดียวกัน** การสร้างใหม่ อุโมงค์ "ชั่วคราว"
ตัวที่สอง หรือค่าเริ่มต้นของหน้า Cloudflare จะตั้งชื่อพี่น้องให้เอง —

`api-yourapp` กับ `api2-yourapp`

— แล้วทุกอย่างดูปกติ โปรเซสอุโมงค์ขึ้น DNS เขียว Pages Function คืน 200
จาก *โฮสต์ใดโฮสต์หนึ่ง* ในขณะที่เบราว์เซอร์เรียกชื่อที่คุณเลิกใช้เมื่อวันอังคารที่แล้ว

กับดักรูปทรงระบบจริงคือคู่นั้นพอดี: **`api-flood` กับ `api2-flood`** เลข
`2` ไม่ใช่เวอร์ชัน เป็นของเหลือ grep ทั้งต้นไม้ — Pages Function,
`wrangler.toml` `[vars]`, ingress ของอุโมงค์ ระเบียน DNS URL ตรวจสุขภาพ
เอกสารที่คุณจะเปิดตอนตีสอง — หาทุกชื่อโฮสต์ API ก่อนประกาศว่าใช้งานจริง
ใส่ชื่อที่เป็น canonical ใน env (`API_ORIGIN=` ว่างใน git) แล้วอย่า hardcode
สองชื่อ

วางยา fallback ที่เงียบ: `cloudflared tunnel run <name>` โดยไม่มี `--config`
จะถอยไปไฟล์กลาง **แล้วสองอุโมงค์จะทับ routing ของกันและกันโดยไม่มี error**
ให้อุโมงค์แต่ละตัวมีไฟล์คอนฟิกของตัวเอง อ้างด้วย `--config` ชัดเจน ทำไฟล์กลาง
ให้เฉื่อย (`http_status:404` อย่างเดียว)

---

## 8.3 Backup paths must not be autobiography / path สำรองต้องไม่ใช่ประวัติชีวิต

**EN.** A backup job that writes to `/Users/<your-name>/…` or to a volume
that only exists on one desk is not a backup. It is a souvenir. The next
Mac, the next USB disk, the next operator, will silently skip the copy
because the path is not there.

- Put the database path and the backup directory in **env**
  (`DATA_DIR=`, `BACKUP_DIR=`) — names only in git, values only on the
  machine.
- Test **restore** on a blank machine, not just "the copy finished with
  exit 0."
- Keep the nightly backup as its **own** scheduled job, independent of
  whether the server process is healthy.

**TH.** งานสำรองที่เขียนไป `/Users/<ชื่อคุณ>/…` หรือโวลุ่มที่มีแค่บนโต๊ะเดียว
ไม่ใช่การสำรอง เป็นของที่ระลึก Mac เครื่องถัดไป ดิสก์ USB ถัดไป คนดูแลถัดไป
จะข้ามการคัดลอกแบบเงียบ ๆ เพราะ path ไม่อยู่

- ใส่ path ฐานข้อมูลและโฟลเดอร์สำรองใน **env** (`DATA_DIR=`, `BACKUP_DIR=`)
  — ใน git มีแต่ชื่อ บนเครื่องมีค่า
- ทดสอบ **การกู้คืน** บนเครื่องเปล่า ไม่ใช่แค่ "คัดลอกเสร็จ exit 0"
- เก็บงานสำรองรายคืนเป็นจ็อบตามตารางของ **ตัวเอง** ไม่ผูกกับสุขภาพของโปรเซสเซิร์ฟเวอร์

---

## 8.4 Historical SQLite cannot be rebuilt from APIs / SQLite ประวัติศาสตร์สร้างใหม่จาก API ไม่ได้

**EN.** This is the lesson that costs years if you learn it in a flood.

Live public feeds give you **now**, plus a short window. They do not give
you last monsoon’s watch scores, your event log, your soil-memory series,
or the tap of every fetch that succeeded or failed. Those live in **your**
append-only database.

Re-cloning the git repo, re-fetching ThaiWater, re-hitting Open-Meteo —
none of that reconstructs history you already computed. Treat the SQLite
file as an artifact **independent of git**. Back it up. Restore-test it.
Do not keep the only copy on the same volume as the running process.

A related failure: a process can be perfectly alive and ingesting nothing.
Health checks that only ask "is port 3000 open?" manufacture confidence.
Report **last successful ingest per source**, and the age of the newest
row. A flood collection loop that has been dead for a month while an
archive grows underneath it is worse than a crash you can see.

Disk is the shared failure mode. A readings table growing tens of
megabytes a day is invisible until `ENOSPC` crash-loops **every** service
on the machine, including the one that would have told you. Alert at 85%
full, rotate logs, put long archives on a configurable path.

**TH.** นี่คือบทเรียนที่เสียปี ถ้าไปเรียนรู้ตอนน้ำท่วม

ฟีดสาธารณะสดให้คุณ **ตอนนี้** บวกหน้าต่างสั้น ๆ ไม่ได้ให้คะแนนเฝ้าระวังฤดูมรสุมที่แล้ว
ล็อกเหตุการณ์ อนุกรมความจำของดิน หรือท่อข้อมูลทุกครั้งที่ดึงสำเร็จหรือล้มเหลว
สิ่งเหล่านี้อยู่ในฐานข้อมูลแบบเพิ่มอย่างเดียว **ของคุณ**

clone git ใหม่ ดึง ThaiWater ใหม่ ยิง Open-Meteo ใหม่ — ไม่มีอะไรในนั้นสร้างประวัติ
ที่คุณคำนวณแล้วกลับมา ถือไฟล์ SQLite เป็นชิ้นงาน **แยกจาก git** สำรองไว้
ทดสอบกู้คืน อย่าเก็บสำเนาเดียวบนโวลุ่มเดียวกับโปรเซสที่รันอยู่

ความล้มเหลวที่เกี่ยวข้อง: โปรเซสมีชีวิตสมบูรณ์แล้วดึงข้อมูลเป็นศูนย์ การตรวจสุขภาพที่ถามแค่ว่า
"พอร์ต 3000 เปิดไหม" สร้างความมั่นใจปลอม รายงาน **ingest สำเร็จล่าสุดต่อแหล่งข้อมูล**
และอายุของแถวล่าสุด ลูปเก็บข้อมูลน้ำท่วมที่ตายมาทั้งเดือนขณะที่คลังด้านล่างโตต่อ
แย่กว่าการพังที่มองเห็น

ดิสก์คือโหมดล้มเหลวร่วม ตารางค่าที่วัดได้ที่โตวันละหลายสิบเมกะไบต์มองไม่เห็นจนกว่า
`ENOSPC` จะทำให้ **ทุก** บริการบนเครื่อง crash-loop รวมตัวที่จะบอกคุณ เตือนที่ 85%
หมุนเวียนล็อก วางคลังยาวบน path ที่ตั้งค่าได้

---

## 8.5 Pages Function as a catch-all proxy / Pages Function เป็นพร็อกซีจับทั้งหมด

**EN.** The Function should not know your route list. A catch-all
(`functions/api/[[path]]` or equivalent) forwards `/api/*` to
`API_ORIGIN`. Adding a backend endpoint then requires **no frontend
deploy**. The class of bug this deletes is "I added `/api/dams` and forgot
to whitelist it on the edge."

`wrangler.toml` in this pattern holds **structure**, not secrets:

```toml
# structure only — no account_id, no tokens, no tunnel credentials
name = "your-flood-watch"
pages_build_output_dir = "public"

[vars]
# public-ish names, never secrets. Values for origins belong in
# the dashboard / .env on the machine, not in git if they are private.
```

Never commit Cloudflare account ids, API tokens, or
`~/.cloudflared/*.json` credentials. Those cannot come from git. See
[the setup checklist](09-compute-and-data.md#95-setup-checklist--รายการติดตั้งบนเครื่องเปล่า).

Verify **bytes**, not version strings. An edge node can pin stale
JavaScript under a new `?v=` key if you curl the custom domain too early.
Probe a canonical `*.pages.dev` (or equivalent) first, with a throwaway
`&probe=` query, and compare hashes of the asset body.

**TH.** Function ไม่ควรรู้รายการเส้นทางของคุณ ตัวจับทั้งหมด
(`functions/api/[[path]]` หรือเทียบเท่า) ส่งต่อ `/api/*` ไป `API_ORIGIN`
การเพิ่ม endpoint ฝั่งหลังบ้านจึง **ไม่ต้อง deploy หน้าบ้าน** บักที่ถูกฆ่าคือ
"ฉันเพิ่ม `/api/dams` แล้วลืมอนุญาตที่ขอบเครือข่าย"

`wrangler.toml` ในแพทเทิร์นนี้เก็บ **โครง** ไม่ใช่ความลับ — ดูตัวอย่างด้านบน
ห้าม commit Cloudflare account id, API token หรือไฟล์ credentials ของ
`cloudflared` สิ่งเหล่านั้นมาจาก git ไม่ได้ ดู[รายการติดตั้ง](09-compute-and-data.md#95-setup-checklist--รายการติดตั้งบนเครื่องเปล่า)

พิสูจน์ **ไบต์** ไม่ใช่สตริงเวอร์ชัน โหนดขอบอาจปัก JavaScript เก่าไว้ใต้คีย์
`?v=` ใหม่ ถ้าคุณ curl โดเมนจริงเร็วเกินไป ยิง `*.pages.dev` (หรือเทียบเท่า)
ก่อน ด้วย `&probe=` ที่ใช้แล้วทิ้ง แล้วเทียบแฮชของตัวไฟล์

---

## 8.6 Embeddings and RAG are optional — and easy to get wrong / embeddings กับ RAG เป็นทางเลือก — และพลาดง่าย

**EN.** A flood watch does not need a vector database. It needs honest
numbers. If you add a chat or "ask the knowledge base" layer:

1. **Ground answers in your readings table first.** A model that
   estimates a water level is worse than no chat. Same rule as
   [§2.5](02-architecture.md) and [§7](07-honest-limitations.md).
2. **Bring your own corpus.** Do not copy anyone else's PDFs, notes, or
   embedding blobs. The private twin's knowledge files are not in this
   repository on purpose.
3. **Provider reality.** Cloud embedding APIs work until the key dies or
   the bill arrives — keep the key in env (`EMBEDDING_API_KEY=`), never in
   git. Local ONNX / Chroma-style indexes can be sound in concept and
   still **unfit for one busy Mac** (disk measured in terabytes is a
   deployment-shape failure, not a patch). Browser-side embeddings
   (Transformers.js) avoid a server key and also avoid becoming load-
   bearing ingest. Pick a path you can run when the network is down.
4. **Label it.** Retrieved prose is not a gauge reading. Do not let a
   similarity hit wear the same badge as ThaiWater.

If the optional path is not ready, ship a bilingual searchable FAQ that
quotes **your published formula**. That is RAG enough for a provincial
briefing room.

**TH.** ระบบเฝ้าระวังน้ำท่วมไม่ต้องการฐานข้อมูลเวกเตอร์ ต้องการตัวเลขที่ซื่อสัตย์
ถ้าจะเพิ่มแชทหรือชั้น "ถามฐานความรู้":

1. **ยึดคำตอบจากตารางค่าที่วัดได้ของคุณก่อน** โมเดลที่ประมาณระดับน้ำแย่กว่าไม่มีแชท
   กฎเดียวกับ[หัวข้อ 2.5](02-architecture.md) และ[หัวข้อ 7](07-honest-limitations.md)
2. **ใช้คลังของคุณเอง** อย่าคัดลอก PDF บันทึก หรือไฟล์ embedding ของคนอื่น
   ไฟล์ความรู้ของคู่แฝดส่วนตัวไม่อยู่ใน repository นี้โดยตั้งใจ
3. **ความจริงของ provider** API embedding บนคลาวด์ใช้ได้จนกว่าคีย์ตายหรืองบมา —
   เก็บคีย์ใน env (`EMBEDDING_API_KEY=`) ห้ามอยู่ใน git ดัชนี ONNX / Chroma ท้องถิ่น
   แนวคิดถูกได้ แต่ **ไม่เหมาะกับ Mac เครื่องเดียวที่ยุ่งอยู่แล้ว** (ดิสก์ระดับเทราไบต์
   คือความล้มเหลวของรูปทรงการติดตั้ง ไม่ใช่บั๊กที่แพตช์) embedding ในเบราว์เซอร์
   (Transformers.js) เลี่ยงคีย์เซิร์ฟเวอร์ และเลี่ยงการเป็น ingest หลัก เลือกทางที่รันได้ตอนเน็ตล่ม
4. **ติดป้าย** ข้อความที่ค้นคืนมาไม่ใช่ค่ามาตรวัด อย่าให้ผล similarity ใส่แบดจ์เดียวกับ ThaiWater

ถ้าทางเลือกยังไม่พร้อม ส่ง FAQ สองภาษาที่ค้นได้ซึ่งอ้าง **สูตรที่คุณเผยแพร่เอง**
นั่นคือ RAG ที่พอสำหรับห้องบรีฟจังหวัด

---

## 8.7 One-Mac operations, in practice / การดูแลบนเครื่องเดียว ในทางปฏิบัติ

**EN.**

- **Do not leave a terminal window open and call it production.** Use
  the OS supervisor (`launchd` on macOS, `systemd` on a VPS) with
  `KeepAlive` / `Restart=always`.
- **Prevent sleep** on the ingest machine, or accept that live pipes
  freeze while the CDN UI stays up.
- **Jitter and backoff** per source ([§2.1](02-architecture.md)). Nine
  government APIs hit on the same second is how you get blocked.
- **Watch the thing that fails.** Last ingest per source, disk of the
  *data* volume (on modern macOS that is not `/`), tunnel hostname
  actually serving the Pages Function.
- **Cadence is not latency.** A feed that *updates* every 10 minutes
  can still be 10–60 minutes old in the UI. Show age, not a green dot
  that means "we have a process."
- **[flood.nonarkara.org](https://flood.nonarkara.org) is not your
  staging API.** Do not point your adapters at it. Point them at the
  public sources in [the catalog](03-data-sources.md).

**TH.**

- **อย่าเปิดหน้าต่างเทอร์มินัลทิ้งไว้แล้วเรียกว่า production** ใช้ตัวดูแลของ OS
  (`launchd` บน macOS, `systemd` บน VPS) พร้อม `KeepAlive` / `Restart=always`
- **กันเครื่องหลับ** บนเครื่อง ingest หรือยอมรับว่าท่อสดจะหยุด ขณะที่ UI บน CDN ยังขึ้น
- **jitter และ backoff** ต่อแหล่ง ([หัวข้อ 2.1](02-architecture.md)) API รัฐเก้าตัวในวินาทีเดียวกันคือทางถูกบล็อก
- **ดูสิ่งที่พังจริง** ingest ล่าสุดต่อแหล่ง ดิสก์ของโวลุ่ม *ข้อมูล* (บน macOS รุ่นใหม่ไม่ใช่ `/`)
  ชื่อโฮสต์อุโมงค์ที่ Pages Function ใช้อยู่จริง
- **ความถี่ไม่ใช่ความหน่วง** ฟีดที่ *อัปเดต* ทุก 10 นาที บน UI อาจเก่า 10–60 นาที แสดงอายุ
  ไม่ใช่จุดเขียวที่แปลว่า "มีโปรเซส"
- **[flood.nonarkara.org](https://flood.nonarkara.org) ไม่ใช่ API สเตจของคุณ**
  อย่าชี้ adapter ไปที่นั่น ชี้ไปแหล่งสาธารณะใน[แคตตาล็อก](03-data-sources.md)

---

## 8.8 What this document is not / สิ่งที่เอกสารนี้ไม่ใช่

**EN.** Not a runbook for the private twin. Not permission to republish
FloodDash. Not a list of Non's hostnames, tokens, or database files. If a
step needs a secret, the secret lives on **your** machine, in **your**
env, under **your** account.

**TH.** ไม่ใช่สมุดปฏิบัติการของคู่แฝดส่วนตัว ไม่ใช่สิทธิ์ในการเผยแพร่ FloodDash
ซ้ำ ไม่ใช่รายการโฮสต์ โทเคน หรือไฟล์ฐานข้อมูลของใคร ถ้าขั้นตอนไหนต้องการความลับ
ความลับนั้นอยู่บนเครื่อง **ของคุณ** ใน env **ของคุณ** ภายใต้บัญชี **ของคุณ**

Next: what to stand up, which public URLs to fetch, and a blank-machine
checklist — [Compute kit and data](09-compute-and-data.md).

---

[← Honest Limitations](07-honest-limitations.md) · [Next: Compute & data →](09-compute-and-data.md)
