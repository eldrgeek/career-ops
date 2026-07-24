# Mode: qualify — Prospect Qualification (A-F Scoring)

Score a prospect company against INTOO's Ideal Customer Profile using 6 evaluation blocks.

## Prerequisites

Before qualifying, read:
1. `intoo-profile.yml` — INTOO ICP, value props, competitors, case studies
2. `data/prospect-pipeline.md` — find the prospect entry and signal details
3. `trigger-sources.yml` — ICP filter criteria
4. The prospect's signal report if one exists in `reports/`

## Scoring Rubric

Six blocks, 1–5 scale each, weighted average = Global Score.

### Block A — Urgency Signal (30% weight)

How recent and strong is the trigger event?

**Base score by signal type:**
| Signal Type | Base Score |
|-------------|-----------|
| Confirmed layoff announcement (press release, SEC filing) | 5.0 |
| News report citing "sources" or "reportedly" | 4.0 |
| Earnings call mention of "restructuring" / "workforce optimization" | 3.5 |
| Hiring freeze announcement | 3.0 |
| M&A announcement (predictive, not yet layoff) | 2.5 |
| Rumor / unverified social media | 2.0 |

**Apply Urgency Decay Multiplier (capped at 5.0):**
| Days Since Signal | Multiplier |
|-------------------|-----------|
| 0–3 | x1.5 |
| 4–7 | x1.2 |
| 8–30 | x1.0 |
| 31–90 | x0.7 |
| 91+ | x0.3 |

Formula: `A_score = min(5.0, base_score * decay_multiplier)`

### Block B — ICP Fit (25% weight)

Score how well the company matches INTOO's ideal customer profile.

| Factor | 5 | 3 | 1 |
|--------|---|---|---|
| Employee count | 1,000–10,000 | 500–999 or 10,001–25,000 | <500 or >25,000 |
| Vertical | Priority vertical (tech, finance, healthcare, manufacturing) | Adjacent (retail, professional services) | Non-target (government, non-profit) |
| Geography | US/Canada | UK/Australia | Other |
| Prior outplacement | No known vendor (greenfield) | Unknown | Known incumbent |

B_score = average of the 4 factors above.

Use WebSearch to find:
- Company employee count (LinkedIn, Wikipedia, Crunchbase)
- Company vertical/industry
- Company HQ location

### Block C — Deal Size Estimate (20% weight)

Estimate the potential contract value.

**Formula:** `estimated_deal = layoff_headcount * per_seat_estimate`

Per-seat estimate (from `intoo-profile.yml`): $1,500–$5,000 depending on service level.
Use midpoint ($3,250) as default.

| Estimated Deal | Score |
|----------------|-------|
| >$500K | 5.0 |
| $250K–$500K | 4.0 |
| $100K–$250K | 3.0 |
| $50K–$100K | 2.0 |
| <$50K | 1.0 |

### Block D — Decision-Maker Access (15% weight)

Can we reach the buyer?

Use WebSearch to find:
- CHRO, Chief People Officer, or VP HR name
- Their LinkedIn profile URL
- Their tenure at the company (longer tenure = more authority)
- Their prior company (did they use outplacement there?)

| Access Level | Score |
|-------------|-------|
| Named CHRO/CPO with LinkedIn, email findable | 5.0 |
| Named VP HR with LinkedIn | 4.0 |
| HR leadership page exists but names unclear | 3.0 |
| Only generic "HR Department" contact | 2.0 |
| No HR leadership info findable | 1.0 |

### Block E — Competitive Position (10% weight)

| Situation | Score |
|-----------|-------|
| Greenfield — no known outplacement vendor | 5.0 |
| Unknown — no public info on vendor | 4.0 |
| Incumbent present but with negative signals (reviews, complaints) | 3.5 |
| Known incumbent, no displacement signals | 2.0 |
| Known incumbent with recent contract renewal | 1.0 |

Use WebSearch to check for mentions of Lee Hecht Harrison, Right Management, Challenger Gray, or Randstad RiseSmart in connection with the company.

### Block F — Engagement Plan (Narrative, Not Scored)

Based on blocks A–E, write:
1. **Recommended first touch:** LinkedIn connection request, InMail, cold email, or warm intro
2. **Lead angle:** Which INTOO value prop resonates most for this prospect?
3. **Case study to reference:** Which case study from `intoo-profile.yml` is closest match?
4. **Timing:** Contact today, within 48 hours, or add to nurture?
5. **Risk factors:** What could make this prospect a waste of time?

## Global Score Calculation

```
Global = (A * 0.30) + (B * 0.25) + (C * 0.20) + (D * 0.15) + (E * 0.10)
```

## Score Thresholds

| Score | Classification | Action |
|-------|---------------|--------|
| 4.5+ | Hot | Generate outreach immediately. Contact today. |
| 4.0–4.4 | Warm | Generate outreach. Contact within 48 hours. |
| 3.5–3.9 | Qualified | Add to nurture sequence. Generate outreach on request. |
| <3.5 | Pass | Log in pipeline as "Pass". Do NOT generate outreach. |

## Output Format

Write report to `reports/{num}-{company-slug}-{date}.md`:

```markdown
# Account Report: {Company} — {date}
**Signal:** {brief description of trigger}
**Signal Date:** {YYYY-MM-DD}
**Score:** {X.X}/5
**Stage:** {Hot|Warm|Qualified|Pass}
**Primary Contact:** {name, title, LinkedIn URL}
**URL/Source:** {link to news or filing}

---

## A) Urgency Signal — {A_score}/5 (weight: 30%)
{Analysis with decay calculation}

## B) ICP Fit — {B_score}/5 (weight: 25%)
{Analysis table}

## C) Deal Size Estimate — {C_score}/5 (weight: 20%)
{Headcount * per-seat calculation}

## D) Decision-Maker Access — {D_score}/5 (weight: 15%)
{Contact research results}

## E) Competitive Position — {E_score}/5 (weight: 10%)
{Vendor intelligence}

## F) Engagement Plan
{Narrative recommendation}

---

**Global Score: {weighted_average}/5 — {Hot|Warm|Qualified|Pass}**
```

## Post-Qualification

1. Update `data/prospect-pipeline.md` — set Score and Stage
2. If score >= 3.5: suggest running `/intoo-leadgen outreach {company}`
3. If score < 3.5: log as "Pass" with reason in Notes column
4. If score >= 4.5 and days-since-signal <= 3: flag as URGENT in notes

## Rules

- NEVER inflate scores to generate more outreach. Be honest.
- NEVER guess employee count — use WebSearch to verify.
- If critical data is unavailable (e.g., can't find employee count), score that block as 2.5 and note "unverified".
- Always cite sources for every factual claim.
