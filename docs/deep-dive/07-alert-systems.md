# Deep dive 7: Alert systems and public warning architecture
## เจาะลึก 7: ระบบแจ้งเตือนและสถาปัตยกรรมการเตือนภัยสาธารณะ

[← Previous](06-open-source-stack.md) · [Deep-dive index](README.md) · [Next →](08-roadmap-and-economics.md)

---

**EN.** FloodDash v1 deliberately ships as **screen-only alerts** — a
choice documented in the main blueprint's
[honest limitations](../07-honest-limitations.md): the dashboard shows risk
clearly but does not push notifications to anyone's phone. This document
is for anyone taking the challenge further, into an actual public-warning
system — a much higher-stakes undertaking with its own established
science.

**TH.** FloodDash v1 ตั้งใจส่งมอบเป็น **การแจ้งเตือนบนหน้าจอเท่านั้น** —
ทางเลือกที่บันทึกไว้ใน[ข้อจำกัดอย่างซื่อสัตย์](../07-honest-limitations.md)
ของพิมพ์เขียวหลัก: แดชบอร์ดแสดงความเสี่ยงชัดเจนแต่ไม่ส่งการแจ้งเตือนไปยัง
โทรศัพท์ของใคร เอกสารนี้สำหรับใครที่รับความท้าทายไปไกลกว่านั้น สู่ระบบเตือน
ภัยสาธารณะจริง — งานที่มีเดิมพันสูงกว่ามากพร้อมวิทยาศาสตร์ที่จัดตั้งขึ้นแล้ว
ของตัวเอง

## The UNDRR four-pillar framework / กรอบสี่เสาหลักของ UNDRR

**EN.** The UN Office for Disaster Risk Reduction's established framework
for "early warnings for all" breaks a complete warning system into four
pillars, and — critically — **research consistently finds the technical
pillars are usually the easy part; the last-mile pillar is where systems
fail**:

1. **Disaster risk knowledge** — understanding the hazards and
   vulnerabilities in advance (this is what [deep dives 1](01-thailand-flood-context.md)
   and [3](03-hydrological-models.md) are about).
2. **Detection, monitoring, and forecasting** — the sensor networks and
   models (deep dives [2](02-data-sources-deep-dive.md) and
   [4](04-machine-learning.md)).
3. **Warning dissemination and communication** — actually getting the
   warning to people, covered in detail below.
4. **Preparedness and response capability** — do the people receiving the
   warning know what to do, and can they physically act on it (evacuation
   routes, shelters, community drills). **This is consistently the pillar
   post-disaster reviews find weakest** — a technically perfect warning
   that reaches someone with nowhere to go and no plan achieves little.
   Any team building alert *technology* should budget real effort into
   pillar 4's human/institutional side, not just pillars 2–3's technical
   side.

**TH.** กรอบที่จัดตั้งขึ้นของสำนักงานลดความเสี่ยงภัยพิบัติแห่งสหประชาชาติ
สำหรับ "การเตือนภัยล่วงหน้าสำหรับทุกคน" แบ่งระบบเตือนภัยที่สมบูรณ์เป็นสี่
เสาหลัก และ — สำคัญมาก — **งานวิจัยพบอย่างสม่ำเสมอว่าเสาหลักด้านเทคนิคมัก
เป็นส่วนที่ง่าย เสาหลักไมล์สุดท้ายคือจุดที่ระบบล้มเหลว**: 1) ความรู้ความ
เสี่ยงภัยพิบัติ 2) การตรวจจับ เฝ้าติดตาม และพยากรณ์ 3) การเผยแพร่และสื่อสาร
การเตือนภัย 4) ความพร้อมและความสามารถตอบสนอง (นี่คือเสาหลักที่การทบทวนหลัง
ภัยพิบัติพบว่าอ่อนแอที่สุดอย่างสม่ำเสมอ)

## LINE-first alerting: the pragmatic Thai channel / การแจ้งเตือนผ่าน LINE เป็นหลัก: ช่องทางไทยเชิงปฏิบัติ

**EN.** For a Thailand-specific system, **LINE** (not SMS, not a native
app) is the pragmatic first channel to build for a simple reason:
Thailand has among the highest LINE penetration rates in the world, far
exceeding any government app's install base — the failed ~$10M+ mobile
alert app case study noted in [deep dive 1](01-thailand-flood-context.md)
is a direct cautionary tale about betting on app-install reach instead of
meeting people on a platform they already have open. LINE's **Messaging
API** supports official accounts that can broadcast push messages to
subscribed users at a published per-message cost band that is low enough
for province-or-district-scale subscriber counts to be genuinely
affordable for a volunteer or small-NGO project, with richer message
formats (maps, buttons, quick replies) than plain SMS. The practical
architecture: a LINE Official Account, a webhook-integrated backend
(the same event bus already emitting to SSE clients in FloodDash's
architecture can emit to a LINE-push adapter with no structural change),
and a subscription/opt-in flow scoped by province or watched location so
people aren't flooded with alerts for regions they don't care about.

**TH.** สำหรับระบบเฉพาะประเทศไทย **LINE** (ไม่ใช่ SMS ไม่ใช่แอปเนทีฟ) คือ
ช่องทางแรกเชิงปฏิบัติที่ควรสร้างด้วยเหตุผลง่าย ๆ: ไทยมีอัตราการใช้ LINE
สูงที่สุดแห่งหนึ่งของโลก เกินฐานติดตั้งของแอปรัฐบาลใด ๆ มาก — กรณีศึกษาแอป
แจ้งเตือนมือถือที่ล้มเหลวมูลค่ากว่า 10 ล้านดอลลาร์ที่กล่าวถึงใน
[เจาะลึก 1](01-thailand-flood-context.md) เป็นบทเรียนโดยตรงเกี่ยวกับการพนัน
กับการเข้าถึงผ่านการติดตั้งแอปแทนที่จะไปหาผู้คนบนแพลตฟอร์มที่พวกเขาเปิดอยู่
แล้ว **Messaging API** ของ LINE รองรับบัญชีทางการที่กระจายข้อความ push ไป
ยังผู้ใช้ที่สมัครรับในอัตราต้นทุนต่อข้อความที่เผยแพร่ซึ่งต่ำพอสำหรับจำนวน
ผู้ติดตามระดับจังหวัดหรืออำเภอที่จะประหยัดได้จริงสำหรับโครงการอาสาสมัครหรือ
NGO ขนาดเล็ก

## Cell broadcast and CAP: the government-grade channel / การกระจายสัญญาณเซลล์และ CAP: ช่องทางระดับรัฐบาล

**EN.** Thailand's own national emergency alert initiative — commonly
referred to as **Thai Alert / T-Alert** — is built on **cell broadcast
technology**, the same mechanism behind the US Wireless Emergency Alerts
and EU public-warning systems: a message broadcast to every mobile device
in range of specific cell towers simultaneously (not sent
individually per subscriber, so it doesn't queue or congest during a mass
event, and it reaches devices even without app installs or opt-in — a
structural advantage the LINE channel above cannot match). The message
format underlying this class of system is typically **CAP (Common
Alerting Protocol) v1.2**, an OASIS/ITU standard XML schema for
structured warning messages (event type, severity, urgency, certainty,
affected geographic area as a polygon or circle, and effective/expiry
times) designed so a single alert authored once can be simultaneously
distributed across cell broadcast, radio, TV crawl, and web/app channels
by systems that all consume the same CAP feed. **A hobbyist or NGO project
cannot access cell-broadcast infrastructure directly** (it requires
telecom-operator and regulator cooperation) — but authoring alerts in
valid CAP v1.2 format from day one is free and future-proofs your system
to plug into a government cell-broadcast network later without a rewrite,
and several countries' emergency-management agencies publish their CAP
feeds publicly for exactly this kind of downstream consumption.

**TH.** โครงการแจ้งเตือนฉุกเฉินแห่งชาติของไทยเอง — มักเรียกว่า **Thai
Alert / T-Alert** — สร้างบน **เทคโนโลยีกระจายสัญญาณเซลล์** กลไกเดียวกับ
ระบบ Wireless Emergency Alerts ของสหรัฐฯ และระบบเตือนภัยสาธารณะของสหภาพ
ยุโรป: ข้อความกระจายไปยังทุกอุปกรณ์มือถือในระยะของเสาสัญญาณเซลล์ที่ระบุ
พร้อมกัน (ไม่ได้ส่งทีละคนต่อผู้ติดตาม จึงไม่ต่อคิวหรือติดขัดระหว่างเหตุการณ์
ขนาดใหญ่ และเข้าถึงอุปกรณ์ได้แม้ไม่ได้ติดตั้งแอปหรือสมัครรับ) รูปแบบข้อความ
ที่อยู่เบื้องหลังระบบประเภทนี้มักเป็น **CAP (Common Alerting Protocol)
v1.2** มาตรฐาน XML ของ OASIS/ITU สำหรับข้อความเตือนภัยที่มีโครงสร้าง
**โครงการงานอดิเรกหรือ NGO เข้าถึงโครงสร้างพื้นฐานกระจายสัญญาณเซลล์โดยตรง
ไม่ได้** (ต้องอาศัยความร่วมมือจากผู้ให้บริการโทรคมนาคมและหน่วยงานกำกับดูแล)
— แต่การเขียนการแจ้งเตือนในรูปแบบ CAP v1.2 ที่ถูกต้องตั้งแต่วันแรกนั้นฟรี
และเตรียมระบบของคุณให้พร้อมเชื่อมต่อกับเครือข่ายกระจายสัญญาณเซลล์ของรัฐบาล
ในภายหลังโดยไม่ต้องเขียนใหม่

## A four-tier alert cascade / ขั้นการแจ้งเตือนสี่ระดับ

**EN.** Putting the above together, a realistic alert-severity cascade for
a system like FloodDash to grow into:

| Tier | Trigger | Channel | Audience |
|---|---|---|---|
| 1 — Informational | Risk score crosses "watch" band | Dashboard color change only (FloodDash v1's current behavior) | Anyone actively viewing the dashboard |
| 2 — Advisory | Risk score crosses "warning" band, or a threshold-crossing alert opens (see the main blueprint's alert logic) | LINE push to opted-in subscribers in the affected province | Subscribed residents/officials in the specific affected area |
| 3 — Watch/warning | Sustained high risk, or an official RID/TMD warning is issued for the same area (cross-check, never originate independently at this tier) | LINE push + SMS fallback for non-LINE users + local radio coordination | Broader public in the affected district |
| 4 — Emergency | Life-safety threshold (imminent inundation of populated area) | Cell broadcast / T-Alert (requires official-agency partnership) + CAP feed to all downstream consumers | Everyone in the affected cell-tower range, opt-in or not |

The load-bearing principle across every tier: **a community project should
never originate a Tier 3 or 4 alert independently.** Its job is to
surface the underlying data clearly and quickly (Tiers 1–2) and, where it
has credible signal of an emerging emergency, escalate to the official
channels (RID, TMD, DDPM) that hold the authority and accountability for
Tier 3–4 warnings — never to bypass them. See the main blueprint's
[honest limitations](../07-honest-limitations.md) for the full ethical
framing this follows.

**TH.** รวมสิ่งข้างต้นเข้าด้วยกัน ขั้นความรุนแรงการแจ้งเตือนที่สมจริงสำหรับ
ระบบอย่าง FloodDash ให้เติบโตไปสู่ (ดูตารางด้านบน) หลักการที่ยึดถือทุกระดับ:
**โครงการชุมชนไม่ควรริเริ่มการแจ้งเตือนระดับ 3 หรือ 4 โดยอิสระเด็ดขาด**
หน้าที่ของมันคือแสดงข้อมูลพื้นฐานอย่างชัดเจนและรวดเร็ว (ระดับ 1-2) และเมื่อ
มีสัญญาณที่น่าเชื่อถือของเหตุฉุกเฉินที่กำลังก่อตัว ให้ยกระดับไปยังช่องทาง
ทางการ (RID, TMD, DDPM) ที่ถือครองอำนาจและความรับผิดชอบสำหรับการเตือนภัย
ระดับ 3-4 — ไม่ใช่การข้ามพวกเขา ดู[ข้อจำกัดอย่างซื่อสัตย์](../07-honest-limitations.md)
ของพิมพ์เขียวหลักสำหรับกรอบจริยธรรมเต็มรูปแบบที่ตามมา

## References / เอกสารอ้างอิง

- UNDRR (2022). *Early Warnings for All: Executive Action Plan.*
- OASIS (2010). *Common Alerting Protocol Version 1.2.*
- ITU-T (2013). *Cell Broadcast Service for Public Warning.*

---

[← Previous](06-open-source-stack.md) · [Deep-dive index](README.md) · [Next →](08-roadmap-and-economics.md)
