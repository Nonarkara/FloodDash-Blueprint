<!--
SPDX-FileCopyrightText: 2026 Dr Non Arkaraprasertkul
SPDX-License-Identifier: CC-BY-4.0
-->

# 10. From probability to decision / จากความน่าจะเป็นสู่การตัดสินใจ

[← Compute & data](09-compute-and-data.md) · [Back to README](../README.md)

---

**EN.** Every phase before this one produces a *number*: a watch score, a
station band, and eventually a calibrated probability. None of these tells
anyone what to do. Evacuation is expensive, and not moving is usually right.
But a person who cannot walk has to be moved before the water arrives. This
chapter is the missing layer: a rule on a whiteboard that turns a probability
into a decision **per action**, with the cost attached, and a way to grade
that decision honestly.

**TH.** ทุกระยะก่อนหน้านี้ให้ *ตัวเลข* ไม่มีตัวไหนบอกว่าต้องทำอะไร การอพยพ
มีต้นทุนสูง และการไม่ย้ายมักถูกต้อง แต่ผู้ที่เดินเองไม่ได้ต้องถูกย้ายก่อนน้ำมา
บทนี้คือชั้นที่ขาด: กฎบนไวท์บอร์ดที่แปลงความน่าจะเป็นเป็นการตัดสินใจ
**รายการกระทำ** พร้อมต้นทุน และวิธีให้คะแนนการตัดสินใจนั้นอย่างซื่อตรง

## 10.1 Prerequisite: a probability that has earned the name / เงื่อนไขก่อน

A score from 0 to 100 is not a probability. Before this chapter applies you
need a model that outputs P(event within horizon), fitted and scored on days
it has not seen (rolling-origin, no leakage), with:

- a **Brier skill score > 0** against the base rate, and
- a **reliability table** (predicted vs observed per bin) published next to it.

If held-out skill is absent, publish no odds. The production twin gates its
logistic province model this way. Its event is "any non-tidal gauge in the
province reaches HII level 5 within 48 h".

## 10.2 The rule / กฎ

This is the cost–loss model (Thompson 1962; Murphy 1977; Richardson 2000)
with two extra terms that flood warning needs. For one person and one
action:

| | Flood reaches them | It does not |
|---|---|---|
| **Act** | C + (1 − e)·L | C + T |
| **Don't act** | L | 0 |

- **C**: the cost of acting.
- **L**: the loss if the flood reaches an unprotected person.
- **e**: the efficacy of the action.
- **T**: the trust burned by a false alarm.
- **q** (footprint): the share of the action's targets that the *forecast
  event* actually wets.

```
act  ⇔  p  >  p*  =  (C + T) / (q·e·L + T)
```

If p\* ≥ 1, no probability at the forecast's spatial unit can justify the
action. It needs sharper, village-scale evidence (q → 1).

## 10.3 A worked ladder / ตัวอย่างบันได

The values below are **placeholders**, in THB per person. The value of a
statistical life (VSL) is set to 10 M THB and is also a placeholder. Replace
all of them with the figures your agency adopts.

| Action | C | L | e | q | T | Lead | p\* (province) | p\* at q = 1 |
|---|---|---|---|---|---|---|---|---|
| Households lift goods and move cars | 200 | 40,000 | 0.5 | 0.3 | 100 | 6 h | 4.9 % | 1.5 % |
| Move bedridden, oxygen- and dialysis-dependent people | 6,000 | 250,000 | 0.9 | 0.3 | 1,000 | 12 h | 10.2 % | 3.1 % |
| Evacuate everyone | 3,000 | 10,000 | 0.95 | 0.3 | 1,500 | 24 h | never | 40.9 % |

Three lessons follow from the table:

1. **The threshold belongs to the action, not to the flood.** A 12 %
   probability justifies moving dependents and comes nowhere near justifying
   mass evacuation. A precision of about 11 % is optimal for one action and
   ruinous for the other. "How accurate is it?" is the wrong question. The
   right one is "is it worth following, and for which action?"
2. **Targeted movement beats mass evacuation.** On regional odds, only
   actions aimed at the few people who cannot move themselves pay.
3. **The footprint q is the biggest lever.** Knowing *which* villages flood
   (q from 0.3 → 1) cuts every threshold about threefold. Spend effort on
   flood-extent history and terrain (HAND) before spending it on a fancier
   model.

## 10.4 Grading decisions, not forecasts / ให้คะแนนการตัดสินใจ

Relative economic value, computed per action on the held-out (p, y) pairs:

```
V = (E_fixed − E_forecast) / (E_fixed − E_perfect)
```

- **E_fixed** is the cheaper of "always act" and "never act".
- **E_forecast** is the expense of acting whenever p ≥ p\*.
- **E_perfect** is the expense with perfect foresight.

V = 1 is perfect, V = 0 adds nothing, and V < 0 means following the odds
loses money. Also publish:
- the expense in currency per person-day under each strategy;
- the best V any threshold could reach on the same pairs. This is an
  in-sample upper bound, and the gap between it and V is a direct read of
  miscalibration.

## 10.5 Pitfalls / กับดัก

- **"The cost of a miss is infinite."** If that were true, p\* = 0 and you
  would evacuate every day. Put a finite, explicit, arguable number on it.
  Hiding the number does not remove it.
- **Tuning recall directly.** For a calibrated probability, the optimal
  threshold *is* p\*. Put the cost of crying wolf into T, not into an
  ad-hoc cap.
- **The unit mismatch.** P(the region floods) ≠ P(this house floods). If q
  does not appear in your rule, your thresholds are off by a factor of 1/q.
- **Timing.** A 48 h probability does not say *when* in those 48 h. Every
  action has a lead time. The next model up is a discrete-time hazard,
  P(first event in hour h | none before), which gives each action a
  **decide-by clock**: expected arrival minus lead time.
- **Who is where.** Thresholds are per person, but priorities depend on
  exposure. The registries of dependent people (in Thailand, the MOPH
  long-term-care rosters held by อสม. and รพ.สต.) are the exposure layer.
  They are personal health data: arrange access with their owners, and
  aggregate before anything reaches a screen.
- **Orders.** None of this issues one. The words on screen should say "the
  odds have crossed the break-even for …". The mandate to order an
  evacuation stays with DDPM (1784) and local government.

---

[← Compute & data](09-compute-and-data.md) · [Back to README](../README.md)
