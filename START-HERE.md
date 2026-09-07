<!--
SPDX-FileCopyrightText: 2026 Dr Non Arkaraprasertkul
SPDX-License-Identifier: CC-BY-4.0
-->

# START HERE · เริ่มที่นี่

You cloned **FloodDash-Blueprint**. This folder is a public method — architecture, formulas, open-data catalogs, a bilingual design language, and a phased build order. It is **not** the FloodDash source tree.

คุณ clone **FloodDash-Blueprint** โฟลเดอร์นี้คือวิธีสาธารณะ — สถาปัตยกรรม สูตร แคตตาล็อกข้อมูลเปิด ภาษาออกแบบสองภาษา และลำดับสร้างทีละขั้น **ไม่ใช่** ซอร์สของระบบ FloodDash ที่รันอยู่

There is no `src/`. If you came here looking for production code, keys, or a shortcut around building your own pipes, you are in the wrong building. Reconstruct from `docs/`.

ไม่มี `src/` ถ้ามาหาโค้ดที่ใช้งานจริง คีย์ หรือทางลัดข้ามการสร้างท่อข้อมูลเอง — นี่ไม่ใช่ตึกนั้น สร้างใหม่จาก `docs/`

---

## What you got / สิ่งที่ได้มา

| You have / มี | You do not have / ไม่มี |
|---|---|
| The blueprint documents in `docs/` | Production source — this repo is documents-only |
| Open-data catalog, cadences, and field-tested gotchas | Keys, tokens, env **values**, or machine configs |
| Formulas you can write on a whiteboard | The private twin **FloodDash** |
| A bilingual TH/EN design language | Permission to scrape [flood.nonarkara.org](https://flood.nonarkara.org) |
| A Phase 0–5 roadmap | An official warning product (that stays with DDPM / TMD / ONWR) |
| **CC BY 4.0** on these documents — [`LICENSE`](LICENSE) | Any licence on the running implementation |

**TH.** พิมพ์เขียว ≠ ซอร์สโค้ด · คู่แฝดส่วนตัวชื่อ FloodDash ไม่ได้อยู่ใน repository นี้
**EN.** Blueprint ≠ source. The private twin named FloodDash is not distributed here.

---

## After clone, in this order / หลัง clone ให้อ่านตามนี้

1. **This file** — scope, licence, and what not to look for (you are here).
2. [`README.md`](README.md) — ethical use first, then the architecture sketch.
3. [`docs/01-why-and-what.md`](docs/01-why-and-what.md) → [`docs/02-architecture.md`](docs/02-architecture.md) → [`docs/03-data-sources.md`](docs/03-data-sources.md).
4. Only then: science, design language, roadmap, honest limitations, production lessons, compute kit.

Do not open DevTools on the live site looking for code. Do not point adapters at the illustration. The method is in this clone.

อย่าเปิด DevTools ของเว็บจริงเพื่อหาโค้ด อย่าชี้ adapter ไปที่ภาพประกอบ วิธีอยู่ในการ clone นี้

Full reading and build tables live in the [README](README.md#build-your-own-from-these-files--สร้างของคุณเองจากไฟล์เหล่านี้).

---

## If you only have ten minutes / ถ้ามีสิบนาที

Read **Ethical use** in the README, then [`docs/01-why-and-what.md`](docs/01-why-and-what.md) and [`docs/07-honest-limitations.md`](docs/07-honest-limitations.md). That is enough to know what this is, what it is not, and what you must not do when the work sits next to people's safety.

พอที่จะรู้ว่านี่คืออะไร ไม่ใช่อะไร และอะไรที่ห้ามทำเมื่องานอยู่ใกล้ความปลอดภัยของคน

---

## License / สัญญาอนุญาต

`SPDX-License-Identifier: CC-BY-4.0`

These documents are [Creative Commons Attribution 4.0 International](https://creativecommons.org/licenses/by/4.0/). The file scanners (including GitHub) should read is [`LICENSE`](LICENSE) — the official legal code, not a human-readable summary.

เอกสารเหล่านี้ใช้ CC BY 4.0 ไฟล์ที่เครื่องสแกน (รวม GitHub) ควรอ่านคือ [`LICENSE`](LICENSE) ซึ่งเป็นข้อความกฎหมายทางการ ไม่ใช่บทสรุป

**Attribution / การให้เครดิต.** Credit Dr Non Arkaraprasertkul (ดร.นน อัครประเสริฐกุล) and the FloodDash Blueprint project, link this repository and [the licence](LICENSE), and say if you changed the material.

**Scope / ขอบเขต.** CC BY 4.0 covers the *documents* in this repository. It does not grant rights to the private FloodDash implementation.

Suggested credit line:

> FloodDash Blueprint by Dr Non Arkaraprasertkul, [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). https://github.com/Nonarkara/FloodDash-Blueprint

When you have a running version — a province, a municipality, a university, a company, or a careful amateur — send a link. The invitation stands.

ถ้าคุณสร้างเวอร์ชันของตัวเองได้ บอกมา คำเชิญยังอยู่
