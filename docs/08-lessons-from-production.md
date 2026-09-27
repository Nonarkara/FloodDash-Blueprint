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

A dated note from the September 2026 share surge is in §8.9. It sits
on top of §8.1–§8.8. It does not replace them.

บันทึกลงวันที่จากคลื่นการแชร์เดือนกันยายน 2569 อยู่ในหัวข้อ 8.9 มันวางบนหัวข้อ
8.1–8.8 ไม่ได้แทนที่หัวข้อเหล่านั้น

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
checklist — [Compute kit and data](09-compute-and-data.md). The surge
note below is the part to reread before you publish a link people will
forward.

ถัดไป: สิ่งที่ต้องตั้ง URL สาธารณะที่จะดึง และรายการตรวจบนเครื่องเปล่า —
[ชุดคำนวณและข้อมูล](09-compute-and-data.md) บันทึกช่วงคลื่นด้านล่างคือส่วนที่ควรอ่านซ้ำ
ก่อนเผยแพร่ลิงก์ที่คนจะส่งต่อ

---

<a id="89-september-2026"></a>

## 8.9 September 2026 share surge / คลื่นการแชร์ กันยายน 2569

**EN.** These notes are from operating a one-machine flood watch while
people forwarded links, in the September 2026 flood season. They are
generic on purpose. No request rates, no private hostnames beyond the
illustration pair already named in §8.2, no keys, no supervisor plists,
no disk paths from anyone’s machine. A forker can rebuild the *shape*
from this section alone.

**TH.** บันทึกเหล่านี้มาจากการดูแลระบบเฝ้าระวังน้ำท่วมบนเครื่องเดียว ขณะที่คนส่งต่อลิงก์
ในฤดูน้ำท่วมเดือนกันยายน 2569 ตั้งใจให้เป็นบทเรียนทั่วไป ไม่มีอัตราคำขอ ไม่มีชื่อโฮสต์ส่วนตัว
นอกจากคู่ตัวอย่างที่ตั้งชื่อไว้แล้วในหัวข้อ 8.2 ไม่มีคีย์ ไม่มีไฟล์ plist ของตัวดูแลโปรเซส
ไม่มี path ดิสก์จากเครื่องของใคร ผู้ที่ fork สร้าง *รูปทรง* นี้ใหม่ได้จากหัวข้อนี้หัวข้อเดียว

### Cold-start grace for a large database / ให้เวลาอุ่นเครื่องเมื่อฐานข้อมูลใหญ่

**EN.** A large SQLite file is slow at the moment a process first touches
it: the page cache is cold, WAL recovery may still be walking, and the
first real query is nothing like the query you timed yesterday afternoon.
A health check that demands a fast “ready” and then **kills** the process
turns that slowness into a restart storm. Each death returns the database
to cold. `KeepAlive` / `Restart=always` (§8.7 above)
then does exactly what you asked, and the map never warms.

Split the lamp. **Listening** means the process is up and can say
“starting.” **Warm** means the last ingest is fresh and ordinary queries
are cheap again. During a grace window you choose — longer than a cold
open of *your* database file, and stored as an env name on the machine,
empty in git — a slow start is not a failure. The probe must not kill
the process inside that window.

While the file is warming, answer with **light** reads: a stored place
snapshot, the age of the newest row, a plain “warming — last reading at
…”. Do not let the health probe, or a click, start a full risk
recompute. The heavy pass belongs on the scheduler, after the process
is warm.

**TH.** ไฟล์ SQLite ที่ใหญ่จะช้าในจังหวะที่โปรเซสแตะมันครั้งแรก: แคชหน้ายังเย็น
การกู้ WAL อาจยังเดินอยู่ และคิวรีจริงครั้งแรกไม่เหมือนคิวรีที่คุณจับเวลาเมื่อบ่ายวาน
การตรวจสุขภาพที่ต้องการ “พร้อม” แบบเร็วแล้ว **ฆ่า** โปรเซส จะเปลี่ยนความช้านั้นเป็นพายุรีสตาร์ต
ทุกครั้งที่ตาย ฐานข้อมูลกลับไปเย็น `KeepAlive` / `Restart=always`
(หัวข้อ 8.7 ด้านบน) จะทำตามที่คุณสั่งเป๊ะ และแผนที่จะไม่มีวันอุ่น

แยกดวงไฟ **ฟังอยู่** แปลว่าโปรเซสขึ้นและพูดว่า “กำลังเริ่ม” ได้ **อุ่นแล้ว** แปลว่า
ingest ล่าสุดใหม่ และคิวรีธรรมดากลับมาเร็วอีกครั้ง ระหว่างหน้าต่างผ่อนเวลาที่คุณเลือก —
นานกว่าการเปิดไฟล์ฐานข้อมูล *ของคุณ* แบบเย็น และเก็บเป็นชื่อ env บนเครื่อง ว่างใน git —
การเริ่มช้าไม่ใช่ความล้มเหลว ตัวตรวจต้องไม่ฆ่าโปรเซสภายในหน้าต่างนั้น

ขณะไฟล์ยังอุ่น ให้ตอบด้วยการอ่าน **เบา**: สแนปชอตของที่ที่เก็บไว้แล้ว อายุของแถวล่าสุด
ข้อความตรง ๆ ว่า “กำลังอุ่นเครื่อง — ค่าล่าสุดเมื่อ …” อย่าให้ตัวตรวจสุขภาพหรือการคลิก
เริ่มคำนวณความเสี่ยงทั้งก้อน งานหนักเป็นของตัวจัดตาราง หลังจากโปรเซสอุ่นแล้ว

### Light place paths under a share spike / เส้นทางของที่แบบเบา ใต้คลื่นการแชร์

**EN.** A forwarded link is many identical reads of one place. Serve a
snapshot you already stored: last readings, ages, badges. A query
parameter in the `?place=` family ([civic jobs](10-civic-jobs-and-trust.md))
should hit that cheap path.

A cold recompute of the watch score, the cascade, and soil memory on
the request path will queue the whole spike behind the heaviest thing
you run. People who believed the map then see a blank, a spinner, or a
timeout. That costs more trust than omitting a tip
([§10.7](10-civic-jobs-and-trust.md)). The static UI on the CDN can
still paint the place and the honest gap while numbers are stale.
Stale and labelled beats blank.

**TH.** ลิงก์ที่ถูกส่งต่อคือการอ่านที่เดียวกันซ้ำ ๆ จำนวนมาก จงเสิร์ฟสแนปชอตที่คุณเก็บไว้แล้ว:
ค่าล่าสุด อายุ แบดจ์ พารามิเตอร์ในตระกูล `?place=` ([งานที่พลเมืองต้องการ](10-civic-jobs-and-trust.md))
ควรชนเส้นทางถูกเส้นนั้น

การคำนวณคะแนนเฝ้าระวัง กราฟต้นน้ำ–ปลายน้ำ และความจำของดินใหม่ทั้งก้อนบนเส้นทางของคำขอ
จะดึงคลื่นทั้งคลื่นไปรออยู่หลังงานที่หนักที่สุดที่คุณรัน คนที่เชื่อแผนที่จะเห็นความว่าง วงหมุน
หรือหมดเวลา นั่นแลกความไว้ใจมากกว่าการไม่ใส่ทิป
([หัวข้อ 10.7](10-civic-jobs-and-trust.md)) UI แบบไฟล์นิ่งบน CDN ยังวาดที่
และช่องว่างที่ซื่อสัตย์ได้ ขณะที่ตัวเลขเก่า ของเก่าที่มีป้าย ดีกว่าความว่าง

### Media and cache leave the database disk / สื่อและแคชออกจากดิสก์ของฐานข้อมูล

**EN.** Citizen photos, map-tile caches, and HTTP caches grow with
attention. The SQLite file grows with history, and history cannot be
rebuilt from the public APIs (§8.4 above).
Put them on different volumes. `DATA_DIR=` stays the database.
`MEDIA_DIR=` and `CACHE_DIR=` (names only in git) take everything a
spike can fill. An image directory that shares the database volume is
how `ENOSPC` arrives in the middle of the forward you hoped to survive.
Alert on the data volume at the threshold you already chose. Do not
wait for the media folder to teach it to you.

**TH.** ภาพจากประชาชน แคชไทล์แผนที่ และแคช HTTP โตตามความสนใจ ไฟล์ SQLite โตตามประวัติ
และประวัติสร้างใหม่จาก API สาธารณะไม่ได้ (หัวข้อ 8.4 ด้านบน)
วางคนละโวลุ่ม `DATA_DIR=` ยังเป็นฐานข้อมูล `MEDIA_DIR=` และ `CACHE_DIR=`
(ใน git มีแต่ชื่อ) รับทุกอย่างที่คลื่นเติมได้ โฟลเดอร์ภาพที่ใช้โวลุ่มเดียวกับฐานข้อมูล
คือทางที่ `ENOSPC` มาถึงกลางการส่งต่อที่คุณหวังจะรอด เตือนบนโวลุ่มข้อมูลที่เกณฑ์ที่คุณเลือกไว้แล้ว
อย่ารอให้โฟลเดอร์สื่อเป็นคนสอน

### Keep browser opt-outs on the host that serves the map / คงการปฏิเสธของเบราว์เซอร์ไว้บนโฮสต์ที่เสิร์ฟแผนที่

**EN.** A shared link opens one origin: the host that serves the map.
That response is where browser opt-outs belong. A `Permissions-Policy`
that declines camera, microphone, and unsolicited geolocation; a
referrer policy that does not hand the place query to third parties;
and a page that still draws the place when the person has blocked
third-party cookies and refused location. Analytics must not be on the
critical path. A tracker that a strict browser drops should fail
quietly, and the place should remain.

Put the same opt-outs on every hostname that can open the map,
including a deliberate fallback (§ below). A spike mitigation that
stands up a new host and forgets them, or that puts a challenge, a
cookie wall, or an account in front of the place, breaks the promise
in [§10.7](10-civic-jobs-and-trust.md). The aunt’s browser is the
client that matters. Configure the host she actually hits.

**TH.** ลิงก์ที่แชร์เปิดต้นทางเดียว: โฮสต์ที่เสิร์ฟแผนที่ การตอบนั้นคือที่อยู่ของการปฏิเสธของเบราว์เซอร์
`Permissions-Policy` ที่ปฏิเสธกล้อง ไมโครโฟน และตำแหน่งที่ไม่ได้ขอ; นโยบาย referrer
ที่ไม่ส่งคิวรีของที่ให้บุคคลที่สาม; และหน้าที่ยังวาดที่นั้นได้ เมื่อคนบล็อกคุกกี้บุคคลที่สามและปฏิเสธตำแหน่ง
ระบบวิเคราะห์ต้องไม่อยู่บนเส้นทางวิกฤต ตัวติดตามที่เบราว์เซอร์เข้มงวดทิ้ง ควรล้มอย่างเงียบ
และที่นั้นควรยังอยู่

วางการปฏิเสธชุดเดียวกันบนทุกชื่อโฮสต์ที่เปิดแผนที่ได้ รวมโฮสต์สำรองที่ตั้งใจ (หัวข้อด้านล่าง)
การรับมือคลื่นที่ตั้งโฮสต์ใหม่แล้วลืมสิ่งเหล่านี้ หรือวางหน้าท้าทาย กำแพงคุกกี้ หรือบัญชี
ขวางที่นั้น คือการผิดคำสัญญาใน[หัวข้อ 10.7](10-civic-jobs-and-trust.md) เบราว์เซอร์ของป้าคือไคลเอนต์ที่สำคัญ
ตั้งค่าโฮสต์ที่เขาเปิดจริง

### Two public API hostnames, on purpose / ชื่อโฮสต์ API สาธารณะสองชื่อ โดยตั้งใจ

**EN.** §8.2 is the accidental pair. A rebuild mints `api-…` beside
`api2-…`, one of them is a leftover, both lights look green, and the
browser is calling the name you retired. The illustration of that trap
is the pair already written there: `api-flood` and `api2-flood`. The
`2` in that story is not a version.

A resilience pair is the opposite discipline. You *intend* two public
API hostnames. Both are configured. Both can answer a **light** place
or snapshot read. One is canonical (`API_ORIGIN=`). The other is an
explicit fallback (`API_ORIGIN_FALLBACK=`), empty in git, never a second
hardcoded string you forgot. The UI fails over when the canonical host
times out. Both carry the warming honesty and the browser opt-outs.
A fallback that returns a blank map is not resilience.

If you cannot say which name is canonical and which is fallback, you
still have the §8.2 trap. Do not copy anyone else’s tunnel names. The
shape above is enough. Your values stay on your machine.

**TH.** หัวข้อ 8.2 คือคู่ที่เกิดโดยไม่ตั้งใจ การสร้างใหม่จะตั้ง `api-…` ข้าง `api2-…`
ชื่อหนึ่งเป็นของเหลือ ไฟทั้งคู่ดูเขียว และเบราว์เซอร์เรียกชื่อที่คุณเลิกใช้แล้ว ภาพประกอบของกับดักนั้น
คือคู่ที่เขียนไว้ที่นั่นแล้ว: `api-flood` กับ `api2-flood` เลข `2` ในเรื่องนั้นไม่ใช่เวอร์ชัน

คู่เพื่อความทนทานเป็นวินัยตรงข้าม คุณ *ตั้งใจ* ให้มีชื่อโฮสต์ API สาธารณะสองชื่อ ทั้งคู่ถูกตั้งค่า
ทั้งคู่ตอบการอ่านของที่หรือสแนปชอต **แบบเบา** ได้ ชื่อหนึ่งเป็น canonical (`API_ORIGIN=`)
อีกชื่อเป็น fallback ที่ระบุชัด (`API_ORIGIN_FALLBACK=`) ว่างใน git ไม่ใช่สตริงที่สองที่ hardcode
แล้วลืม UI สลับเมื่อโฮสต์หลักหมดเวลา ทั้งคู่พกความซื่อสัตย์เรื่องการอุ่นเครื่อง และการปฏิเสธของเบราว์เซอร์
fallback ที่คืนแผนที่ว่างไม่ใช่ความทนทาน

ถ้าคุณบอกไม่ได้ว่าชื่อไหนเป็นหลัก ชื่อไหนเป็นสำรอง คุณยังอยู่ในกับดักของหัวข้อ 8.2
อย่าคัดลอกชื่ออุโมงค์ของคนอื่น รูปทรงด้านบนเพียงพอ ค่าของคุณอยู่บนเครื่องของคุณ

---

[← Honest Limitations](07-honest-limitations.md) · [Next: Compute & data →](09-compute-and-data.md)
