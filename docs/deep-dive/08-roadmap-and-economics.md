# Deep dive 8: Roadmap and economics
## เจาะลึก 8: แผนงานและเศรษฐศาสตร์

[← Previous](07-alert-systems.md) · [Deep-dive index](README.md)

---

**EN.** The main blueprint's [build-your-own roadmap](../06-build-your-own-roadmap.md)
lays out 6 phases across roughly 8–12 weeks for two developers building a
FloodDash-equivalent from zero. This document goes one level deeper: cost
tables, team composition beyond the initial build, and funding-source
ideas — for anyone whose challenge attempt succeeds and needs to plan
past the prototype stage.

**TH.** [แผนงานสร้างของคุณเอง](../06-build-your-own-roadmap.md)ของพิมพ์เขียว
หลักวางไว้ 6 ระยะตลอดประมาณ 8-12 สัปดาห์สำหรับนักพัฒนาสองคนที่สร้าง
FloodDash-เทียบเท่าจากศูนย์ เอกสารนี้ไปลึกกว่าหนึ่งระดับ: ตารางต้นทุน องค์
ประกอบทีมเกินกว่าการสร้างเริ่มต้น และแนวคิดแหล่งเงินทุน — สำหรับใครที่ความ
พยายามในความท้าทายสำเร็จและต้องวางแผนเกินกว่าขั้นต้นแบบ

## Cost table by phase / ตารางต้นทุนตามระยะ

**EN.** All figures below are **illustrative order-of-magnitude estimates
(single-source)**, built from general market knowledge of cloud pricing
and typical open-source-project costs, not a quote from any vendor —
verify current pricing before committing a budget.

| Phase | What's added | Rough monthly infra cost | Rough one-time cost |
|---|---|---|---|
| 0. Prototype (what this blueprint describes) | Single Mac/PC + Cloudflare Tunnel + free-tier data sources | ~$0 (electricity + existing hardware only) | $0 |
| 1. Small-team pilot (1 province, opt-in users) | Managed small Postgres/TimescaleDB instance, small compute instance, LINE Official Account (free tier covers modest message volume) | ~$25–90/month | Domain name (~$10–15/year), designer time for a distinct visual identity if desired |
| 2. Multi-province deployment | Larger compute + database tier, CDN for map tiles, LINE paid message tier once subscriber count exceeds free-tier message allowance | ~$150–400/month | Translation/localization QA if expanding dialects, additional data-source integration work |
| 3. Institutional partnership (co-development with an agency) | Redundant/HA infrastructure, formal uptime monitoring, security audit | ~$500–1,500/month | Security audit (~$5,000–15,000 one-time, highly variable), legal review of any data-sharing agreement |

**TH.** ตัวเลขทั้งหมดด้านล่างเป็น **ประมาณการลำดับขนาดเชิงอธิบาย (แหล่งเดียว)**
สร้างจากความรู้ตลาดทั่วไปเรื่องราคาคลาวด์และต้นทุนโครงการโอเพนซอร์สทั่วไป
ไม่ใช่ใบเสนอราคาจากผู้ขายใด ๆ — ตรวจสอบราคาปัจจุบันก่อนตั้งงบประมาณ (ดู
ตารางด้านบนสำหรับรายละเอียดต่อระยะ)

## Team composition beyond the initial build / องค์ประกอบทีมเกินกว่าการสร้างเริ่มต้น

**EN.** The main roadmap's 2-developer estimate covers building the
system. Operating it long-term as a trusted public resource needs
different, often part-time, roles:

- **A data-pipeline maintainer** — government APIs change endpoints,
  field names, and authentication requirements without much notice (see
  the "gotchas" scattered through [deep dive 2](02-data-sources-deep-dive.md));
  someone needs to notice a pipeline going quiet and fix the adapter,
  ideally within hours, not weeks — this is the single most
  under-budgeted ongoing role in projects like this.
- **A domain-knowledge reviewer** (part-time, ideally with a hydrology
  or civil-engineering background) — to sanity-check that risk-scoring
  changes, new thresholds, or model updates make physical sense before
  they ship, catching the kind of error a pure-software engineer might
  miss (e.g., a unit mismatch between mm and cm silently doubling every
  displayed rainfall figure).
- **A community/translation coordinator** — if the project grows beyond
  Thai/English into regional dialects or additional countries, someone
  needs to own translation quality and cultural-appropriateness review,
  not just literal accuracy.
- **A designated on-call contact for the "Tier 3/4 escalation" boundary**
  described in [deep dive 7](07-alert-systems.md) — a real person or small
  team who knows exactly who to call at RID/TMD/DDPM if the system
  surfaces a genuine emerging emergency, so that boundary isn't just a
  design document but an actual working relationship.

**TH.** การประมาณนักพัฒนาสองคนของแผนงานหลักครอบคลุมการสร้างระบบ การดำเนิน
งานระยะยาวในฐานะทรัพยากรสาธารณะที่เชื่อถือได้ต้องการบทบาทที่ต่างกัน มักเป็น
พาร์ทไทม์: ผู้ดูแลไปป์ไลน์ข้อมูล (API รัฐบาลเปลี่ยนแปลงโดยไม่ค่อยแจ้งล่วง
หน้า), ผู้ทบทวนความรู้เฉพาะทาง (ตรวจสอบว่าการเปลี่ยนแปลงการให้คะแนนความเสี่ยง
สมเหตุสมผลทางฟิสิกส์), ผู้ประสานงานชุมชน/การแปล, และผู้ติดต่อเวรที่กำหนดไว้
สำหรับขอบเขต "การยกระดับระดับ 3/4" ที่อธิบายใน
[เจาะลึก 7](07-alert-systems.md)

## Funding-source ideas / แนวคิดแหล่งเงินทุน

**EN.** For a project explicitly framed (as this one is, see the main
blueprint's [README](../../README.md)) as a public good rather than a
commercial product, realistic funding paths without compromising that
framing:

- **Disaster-risk-reduction grant programs** — UNDRR, World Bank
  disaster-resilience funds, and regional development banks (Asian
  Development Bank) periodically fund exactly this category of
  early-warning-system tooling, often with an explicit preference for
  open-source, locally-adapted solutions over proprietary vendor
  contracts — a good fit for a project with this one's origin story.
- **University/academic partnership** — a hydrology, civil engineering,
  or computer science department co-publishing validation studies on the
  system (e.g., "how did our risk score perform against the actual 2026
  monsoon season") gets the project legitimacy and access to student
  contributor time, in exchange for genuinely rigorous, honest evaluation
  — not marketing.
- **Corporate social-responsibility sponsorship for infrastructure costs
  only** — e.g., a cloud provider's nonprofit/disaster-relief credit
  program covering hosting costs, structured so the sponsor pays
  infrastructure bills directly rather than the project accepting
  general-purpose funds that could create obligations in tension with
  staying free and open.
- **Small-dollar community/crowdfunding for the specific "last mile"
  gap** — the [UNDRR pillar 4 problem](07-alert-systems.md) (preparedness
  and response capability) is often the least fundable through
  institutional grants (funders prefer novel technology over unglamorous
  community drills and signage) and the best fit for direct small-dollar
  community fundraising tied to a specific, visible local need.

**TH.** สำหรับโครงการที่วางกรอบไว้อย่างชัดเจน (ดังที่นี่ ดู
[README](../../README.md)ของพิมพ์เขียวหลัก) เป็นสินค้าสาธารณะแทนที่จะเป็น
ผลิตภัณฑ์เชิงพาณิชย์ เส้นทางเงินทุนที่สมจริงโดยไม่กระทบกรอบนั้น: โครงการทุน
ลดความเสี่ยงภัยพิบัติ (UNDRR, ธนาคารโลก, ธนาคารพัฒนาเอเชีย), ความร่วมมือ
มหาวิทยาลัย/วิชาการ, การสนับสนุนความรับผิดชอบต่อสังคมขององค์กรสำหรับต้นทุน
โครงสร้างพื้นฐานเท่านั้น, และการระดมทุนชุมชนขนาดเล็กสำหรับช่องว่าง "ไมล์
สุดท้าย" โดยเฉพาะ

## The closing thought / ความคิดปิดท้าย

**EN.** Every number, formula, and citation across these eight documents
exists for one purpose: to lower the barrier for someone — a student, a
small NGO, a provincial IT department, a weekend hacker — to build
something real instead of starting from a blank page or, worse, paying a
vendor millions of baht for something that could have been free. If this
deep-dive saves you research time, spend the time you saved on the part
that actually matters most and is hardest to write down: talking to the
people in Hat Yai, or wherever your flood-prone community is, about what
they actually needed during the last flood and didn't get.

**TH.** ทุกตัวเลข สูตร และการอ้างอิงตลอดแปดเอกสารนี้มีอยู่เพื่อจุดประสงค์
เดียว: ลดอุปสรรคสำหรับใครสักคน — นักศึกษา NGO ขนาดเล็ก หน่วยงาน IT จังหวัด
แฮ็กเกอร์วันหยุดสุดสัปดาห์ — ให้สร้างสิ่งที่แท้จริงแทนที่จะเริ่มจากหน้าว่าง
หรือแย่กว่านั้น จ่ายเงินหลายล้านบาทให้ผู้ขายสำหรับสิ่งที่ควรจะฟรี ถ้าเจาะลึก
นี้ประหยัดเวลาวิจัยของคุณ ใช้เวลาที่ประหยัดได้กับส่วนที่สำคัญที่สุดจริง ๆ
และเขียนลงยากที่สุด: การพูดคุยกับผู้คนในหาดใหญ่ หรือที่ไหนก็ตามที่ชุมชนของ
คุณเสี่ยงน้ำท่วม เกี่ยวกับสิ่งที่พวกเขาต้องการจริง ๆ ระหว่างน้ำท่วมครั้งล่าสุด
และไม่ได้รับ

---

[← Previous](07-alert-systems.md) · [Deep-dive index](README.md)
