# Mode: account-research — Deep Account Research

Comprehensive research on a prospect company to support qualification and outreach. Mirrors `modes/deep.md` but for B2B outplacement context.

## Prerequisites

Before researching, read:
1. `intoo-profile.yml` — INTOO ICP and competitors list
2. `data/prospect-pipeline.md` — existing prospect data
3. Any existing report for this prospect in `reports/`

## Six Research Axes

### Axis 1 — Layoff Details

Use WebSearch to find:
- **Scale:** How many employees affected? What percentage of workforce?
- **Departments:** Which teams/functions were cut? (Engineering, sales, ops, etc.)
- **Public reasons:** Cost-cutting? Restructuring? M&A integration? AI replacement?
- **Timeline:** Was it a single event or phased? Are more rounds expected?
- **Severance:** Any public details on severance packages offered?
- **Prior events:** Has this company done layoffs before? When? How big?

Search queries:
- `"{company}" layoffs {year}`
- `"{company}" restructuring employees`
- `"{company}" workforce reduction details`

### Axis 2 — HR Leadership

Use WebSearch to find:
- **CHRO / Chief People Officer:** Full name, title, LinkedIn URL
- **VP Human Resources:** If no C-level HR, find the most senior HR leader
- **Tenure:** How long have they been at the company?
- **Prior company:** Where were they before? Did that company use outplacement?
- **Public statements:** Have they made any statements about the layoff?
- **Conference appearances:** SHRM, HR Tech, Unleash, etc.

Search queries:
- `"{company}" CHRO OR "Chief People Officer" OR "VP Human Resources" LinkedIn`
- `"{company}" head of HR`

**RULE:** Do NOT scrape LinkedIn profiles. Only use WebSearch results and public pages.

### Axis 3 — Vendor Intelligence

Check if the company has a known outplacement vendor:
- Search for `"{company}" outplacement provider`
- Search for `"{company}" "Lee Hecht Harrison" OR "Right Management" OR "Challenger Gray" OR "Randstad RiseSmart" OR "INTOO"`
- Check SHRM vendor reviews if available
- Check G2 and Gartner Peer Insights for vendor mentions

Record:
- Known incumbent vendor (if any)
- Contract status (recent renewal? Expiring? Unknown?)
- Any negative reviews or complaints about current vendor
- Displacement opportunity score (1–5)

### Axis 4 — Employer Brand Health

Use WebSearch to find:
- **Glassdoor score:** Current rating and trend (improving/declining)
- **Indeed rating:** Current score
- **Blind posts:** Any relevant anonymous employee posts about the layoff
- **Social media:** Twitter/LinkedIn sentiment about the layoff
- **Media coverage tone:** Sympathetic? Critical? Neutral?

This matters because: Companies with declining employer brand scores post-layoff are MORE likely to invest in outplacement to protect their reputation.

### Axis 5 — Financial Health

Can they afford outplacement services?
- **Public company:** Check recent earnings, revenue trend, stock performance
- **Private company:** Check recent funding rounds (Crunchbase), estimated revenue
- **Layoff context:** Cost-cutting (tight budget) vs. strategic restructuring (budget available)
- **Industry context:** Is the whole industry contracting or just this company?

Score:
| Situation | Financial Health Score |
|-----------|----------------------|
| Profitable company doing strategic restructuring | 5/5 |
| Well-funded startup optimizing burn rate | 4/5 |
| Public company with stable revenue, cutting for efficiency | 4/5 |
| Company in declining industry, cutting to survive | 2/5 |
| Company in financial distress (missed earnings, debt) | 1/5 |

### Axis 6 — INTOO Angle

Based on all research above, determine:
1. **Best value prop:** Which INTOO value prop resonates most?
   - Speed → "2.5x faster placement"
   - Scale → "130+ countries, 2,100+ coaches"
   - Access → "Instant live coach access, 7 days/week"
   - Brand → "Employer brand protection"
   - Reporting → "Real-time employer dashboard"
2. **Best case study:** Which case study from `intoo-profile.yml` is closest match by vertical and size?
3. **Ideal hook:** What specific aspect of their situation makes the best opening line?
4. **Objection forecast:** What objections might the CHRO raise? (Budget? Timing? Incumbent?)
5. **Competitive angle:** If there's an incumbent, what's the displacement pitch?

## Output Format

Write research report to `reports/{num}-{company-slug}-{date}-research.md`:

```markdown
# Deep Research: {Company} — {date}
**Prospect #:** {num}
**Signal:** {brief description}
**Signal Date:** {YYYY-MM-DD}

---

## 1. Layoff Details
{findings}

## 2. HR Leadership
{findings with names, titles, LinkedIn URLs}

## 3. Vendor Intelligence
{findings on incumbent or greenfield status}

## 4. Employer Brand Health
{Glassdoor, Indeed, sentiment analysis}

## 5. Financial Health
{Can they afford outplacement? Score and reasoning}

## 6. INTOO Angle
{Recommended value prop, case study, hook, objections}

---

## Research Summary
| Axis | Key Finding | Confidence |
|------|-------------|-----------|
| Layoff Details | ... | High/Medium/Low |
| HR Leadership | ... | High/Medium/Low |
| Vendor Intel | ... | High/Medium/Low |
| Employer Brand | ... | High/Medium/Low |
| Financial Health | ... | High/Medium/Low |
| INTOO Angle | ... | — |

## Recommended Next Step
{Qualify, outreach, nurture, or pass — with reasoning}
```

## Rules

- NEVER scrape LinkedIn profiles directly. Use WebSearch only.
- ALWAYS cite sources (URLs) for every factual claim.
- If information is unavailable, say "Not found" — never fabricate.
- Mark confidence level for each axis (High/Medium/Low).
- Keep research focused on outplacement-relevant data. Skip irrelevant company details.
