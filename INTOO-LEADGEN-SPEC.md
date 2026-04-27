# INTOO Lead Generation System — Specification
**Version:** 0.1.0-draft
**Date:** 2026-04-06
**Based on:** career-ops v1.x (github.com/eldrgeek/career-ops)

---

## Section 0: Background Context

**Problem:** INTOO (intoo.com) sells outplacement and career transition services B2B to HR leaders (CHROs, VP HR, Chief People Officers) at enterprises. Their ideal buyers only *need* the product during layoff/restructuring events — meaning the sales window is narrow (72 hours to 30 days post-announcement) and the signal that opens it is public. The challenge: finding the signal fast, qualifying the prospect, and getting a personalized message in front of the right HR leader before a competitor does.

**Solution:** Adapt career-ops — an AI-powered job-search pipeline built on Claude Code — into an outbound lead generation system. career-ops already has: multi-source scanning (Playwright + WebSearch), structured scoring (A–F evaluation), personalized outreach generation (LinkedIn message + email), pipeline tracking, batch processing, and a terminal dashboard. All of these map directly to INTOO's needs with a content swap rather than a rebuild.

**Key Concepts:**
- **Signal:** Any observable event that indicates a company may need outplacement services (layoff announcement, M&A, restructuring filing, hiring freeze)
- **Prospect:** A company that has emitted a signal AND scores ≥ 3.5 on ICP fit
- **ICP (Ideal Customer Profile):** Companies 500–10,000 employees, primarily tech/finance/healthcare, with an accessible CHRO or VP HR on LinkedIn
- **Urgency Decay:** Outplacement buying decisions concentrate in Days 0–30 post-announcement. Score multipliers reflect this.
- **Survivor Upsell:** After a layoff is complete, the same CHRO is a buyer for career development/mobility services for remaining employees — second revenue opportunity from same event.

**Constraints:**
- Must run on existing career-ops infrastructure (Node.js, Playwright, Claude Code)
- Must not require database or external APIs beyond what career-ops already uses
- Smoke test must use real, public layoff data (not synthetic)
- All outreach generated is for review — never auto-sent
- INTOO team (Mira) provides ICP content, case studies, and value props; system generates the outreach

---

## Non-Negotiables

- MUST never auto-send any outreach. All generated messages are drafts for human review.
- MUST use real public layoff signals for smoke testing (layoffs.fyi, news, etc.)
- MUST preserve all existing career-ops functionality (no breaking changes to original modes)
- MUST be runnable end-to-end by Claude Code with `--dangerously-skip-permissions`
- MUST produce a human-readable Mira Report after smoke test

---

## Out of Scope (v0.1)

- NOT integrating with CRM (Salesforce, HubSpot) — output is markdown files, SDR imports manually
- NOT sending emails or LinkedIn messages automatically
- NOT scraping LinkedIn profiles (ToS risk) — contacts are found via WebSearch
- NOT real-time alerting (push notifications) — system is run on-demand or scheduled
- NOT modifying the Go TUI dashboard binary (relabeling is out of scope for v0.1, add to v0.2 backlog)
- NOT multilingual outreach (English only for v0.1)

---

## Success Criteria

### Phase 1 — career-ops Smoke Test
- [ ] `npm install` completes cleanly
- [ ] `config/profile.yml` created from example
- [ ] `cv.md` created (minimal stub for testing)
- [ ] Sample JD evaluation runs end-to-end via `/career-ops {JD text}`
- [ ] Report `.md` generated in `reports/`
- [ ] PDF generated in `output/`

### Phase 2 — New Module Creation
- [ ] `intoo-profile.yml` created with INTOO ICP, value props, competitors
- [ ] `trigger-sources.yml` created with ≥ 5 active signal sources
- [ ] `modes/signal-scan.md` created and self-consistent
- [ ] `modes/qualify.md` created with 6-block A–F scoring rubric
- [ ] `modes/outreach.md` created with LinkedIn + email variants
- [ ] `modes/account-research.md` created
- [ ] `modes/survivor-pitch.md` created
- [ ] `data/prospect-pipeline.md` created (empty tracker)
- [ ] `templates/states-intoo.yml` created with 10 pipeline states
- [ ] `.claude/skills/intoo-leadgen/SKILL.md` created (skill router)
- [ ] `INTOO-CLAUDE.md` created (top-level context for INTOO fork)

### Phase 3 — Smoke Test (Lead Gen)
- [ ] Signal scan finds ≥ 3 real recent layoff events
- [ ] At least 1 prospect qualified with score ≥ 3.5 and all 6 blocks populated
- [ ] LinkedIn outreach message generated (≤ 300 chars) for that prospect
- [ ] SDR email generated for that prospect
- [ ] Prospect added to `data/prospect-pipeline.md`

### Phase 4 — Mira Report
- [ ] `reports/INTOO-SMOKE-TEST-REPORT.md` generated
- [ ] Report contains: what was built, smoke test results, what Mira must provide, next steps

---

## File Structure (New Files)

```
career-ops/
├── INTOO-CLAUDE.md                          ← NEW: top-level context for INTOO fork
├── INTOO-LEADGEN-SPEC.md                    ← this file
├── INTOO-BUILD-TASK.md                      ← Claude Code task document
├── intoo-profile.yml                        ← NEW: ICP + value props (like cv.md)
├── trigger-sources.yml                      ← NEW: signal sources (like portals.yml)
├── modes/
│   ├── signal-scan.md                       ← NEW: layoff signal scanner
│   ├── qualify.md                           ← NEW: prospect scoring (A–F)
│   ├── outreach.md                          ← NEW: SDR message generator
│   ├── account-research.md                  ← NEW: deep account research
│   └── survivor-pitch.md                    ← NEW: post-layoff upsell mode
├── data/
│   └── prospect-pipeline.md                 ← NEW: prospect tracker (like applications.md)
├── templates/
│   └── states-intoo.yml                     ← NEW: pipeline states
└── .claude/skills/intoo-leadgen/
    └── SKILL.md                             ← NEW: skill router
```

---

## Data Models

### Prospect Pipeline Entry (`data/prospect-pipeline.md`)

```markdown
| # | Signal Date | Company | Scale | Vertical | Score | Stage | Contact | Report | Notes |
|---|-------------|---------|-------|----------|-------|-------|---------|--------|-------|
| 001 | 2026-04-02 | Acme Corp | 500 | Tech | 4.3/5 | Contacted | VP HR: Jane D. | [001](reports/001-acme-2026-04-02.md) | AI restructuring |
```

### Account Report Header (`reports/001-{company}-{date}.md`)

```markdown
# Account Report: {Company} — {date}
**Signal:** {brief description of trigger}
**Signal Date:** {YYYY-MM-DD}
**Score:** {X.X}/5
**Stage:** {pipeline stage}
**Primary Contact:** {name, title, LinkedIn}
**URL/Source:** {link to news or filing}
```

### trigger-sources.yml Structure

```yaml
signal_sources:
  - name: "layoffs_fyi"
    type: scrape
    url: "https://layoffs.fyi"
    enabled: true
    priority: high
    
  - name: "tech_layoffs_crunchbase"
    type: websearch
    query: "site:crunchbase.com layoffs 2026"
    enabled: true
    priority: medium
    
  - name: "sec_edgar_8k"
    type: websearch
    query: 'site:sec.gov "workforce reduction" OR "headcount reduction" 8-K 2026'
    enabled: true
    priority: high

title_filter:
  # Companies below this size: skip
  min_employees: 500
  # Industries to prioritize (others still qualify, just lower score)
  priority_verticals: [tech, finance, healthcare, manufacturing, retail]
  # Exclude known non-buyers
  exclude_patterns: ["solo founder", "seed stage", "non-profit"]
```

### intoo-profile.yml Structure

```yaml
company:
  name: INTOO
  tagline: "Compassionate career transitions — 2.5× faster job placement"
  website: "intoo.com"
  
value_props:
  primary:
    - "Instant live coach access — 7 days/week, no chatbots, no wait"
    - "2.5× faster placement vs. industry average"
    - "130+ countries, 2,100+ coaches, local language"
    - "Real-time employer reporting dashboard"
    - "Employer brand protection during reductions"
  by_company_size:
    mid_market: "Flexible per-employee pricing — no minimums"
    enterprise: "Dedicated Client Success team + custom reporting"

icp:
  size_range: "500–10,000 employees"
  best_verticals: [tech, finance, healthcare, manufacturing]
  buyer_personas:
    primary: ["CHRO", "Chief People Officer", "VP Human Resources"]
    secondary: ["HR Business Partner", "Director Total Rewards", "VP Talent"]
  geography: "North America primary, global available"
  timing_sweet_spot: "0–30 days post-announcement"
  
competitors:
  - name: "Lee Hecht Harrison"
    displacement_angle: "High-touch coaching vs. their scaled delivery model"
  - name: "Challenger Gray & Christmas"
    displacement_angle: "Technology platform + instant access vs. traditional scheduling"
  - name: "Right Management"
    displacement_angle: "Coach quality consistency and 7-day availability"
  - name: "Randstad RiseSmart"
    displacement_angle: "Human coaching first vs. AI-heavy approach"
    
case_studies:
  # Mira to fill these in with real data
  - vertical: tech
    description: "TBD — provide: company size, employees transitioned, avg weeks to placement"
  - vertical: finance
    description: "TBD"
```

---

## Scoring Rubric (`modes/qualify.md`)

Six blocks, 1–5 scale each, weighted average = Global Score:

| Block | Name | Weight | Description |
|-------|------|--------|-------------|
| A | Urgency Signal | 30% | How strong/recent is the trigger? Day 0 = 5.0, Day 30+ = 1.0 |
| B | ICP Fit | 25% | Size, vertical, geography vs. INTOO sweet spot |
| C | Deal Size | 20% | Estimated headcount × avg contract → deal tier |
| D | Decision-Maker Access | 15% | CHRO/VP HR visible on LinkedIn? Email findable? |
| E | Competitive Position | 10% | Greenfield vs. known incumbent + displacement signal |
| F | Engagement Plan | — | Narrative only — recommended approach, not scored |

**Urgency Decay Multipliers:**

```
Day 0–3:   × 1.5  (announcement week — highest urgency)
Day 4–7:   × 1.2  (vendor selection starting)
Day 8–30:  × 1.0  (normal — still in buying window)
Day 31–90: × 0.7  (warm — may have decided already)
Day 91+:   × 0.3  (cold — flag for survivor upsell instead)
```

**Score Thresholds:**

```
4.5+ → Hot: outreach today
4.0–4.4 → Warm: outreach within 48 hours
3.5–3.9 → Qualified: add to nurture sequence
< 3.5 → Pass: not worth SDR time this cycle
```

---

## Outreach Templates

### LinkedIn Connection Request (≤ 300 chars)
```
Hook (specific to their situation) + Proof (one INTOO stat relevant to their pain) + Ask (15-min call)
Example: "Saw the [Company] announcement this week. We helped [Similar Co] transition [N] people in [timeframe] — 2.5× faster than average. Worth a 15-min call to walk through how we work?"
```

### Cold Email (SDR)
```
Subject: [Company] team transition — quick note
Body: 3 paragraphs max
  P1: Specific hook (their announcement)
  P2: Relevant proof (case study or stat)
  P3: Low-friction ask (demo link or Calendly)
```

---

## Pipeline States (`templates/states-intoo.yml`)

```yaml
states:
  - Signal:      "New trigger detected, not yet qualified"
  - Qualified:   "Scored ≥ 3.5, active outreach approved"
  - Contacted:   "First touch sent (LinkedIn/email)"
  - Responded:   "Prospect replied"
  - Meeting:     "Discovery call scheduled"
  - Proposal:    "Proposal submitted"
  - Negotiating: "In contract discussion"
  - Won:         "Contract signed"
  - Lost:        "Lost to competitor or no-decision"
  - Nurture:     "Good ICP, wrong timing — follow up in 60 days"
  - Watch:       "Pre-signal flag — monitoring, no announcement yet"
```

---

## Smoke Test Protocol

### Real Signals to Use (as of April 2026)
The smoke test should search for recent layoff events from April 2026. Use WebSearch to find:
1. Any tech company announcing layoffs in the last 30 days
2. Any company announcing restructuring or "workforce optimization" in earnings calls

### Expected Output
After smoke test completes, `reports/INTOO-SMOKE-TEST-REPORT.md` should contain:
- Number of signals found
- Top 3 qualified prospects with scores
- Sample outreach message for #1 prospect
- Assessment: what worked, what needs tuning
- What Mira must provide before production use

---

## Safety Invariants

1. **No auto-send:** Outreach files are written to `reports/` and `data/` only. No email API, no LinkedIn API. SDR copies manually.
2. **No private data scraped:** Only public sources (news, SEC, layoffs.fyi, company websites). No LinkedIn profile scraping.
3. **Human review gate:** The system recommends; Mira/SDR decides. Score < 3.5 = system will not generate outreach (mirrors career-ops ethical use rule).
4. **No breaking career-ops:** All new files are additive. Original modes untouched.
5. **Audit trail:** Every prospect has a report file with sources cited. Nothing generated from thin air.

---

## Implementation Roadmap

### Phase 1: Smoke Test career-ops ⏳ 2026-04-06
- [ ] Setup: create `config/profile.yml`, `cv.md` stubs
- [ ] Run evaluation on 1 sample JD
- [ ] Verify report + PDF generated
- [ ] Status: PASS / FAIL + notes

### Phase 2: Build New Modules ⏳ 2026-04-06
- [ ] Write `intoo-profile.yml` (stub — Mira to fill)
- [ ] Write `trigger-sources.yml`
- [ ] Write `modes/signal-scan.md`
- [ ] Write `modes/qualify.md`
- [ ] Write `modes/outreach.md`
- [ ] Write `modes/account-research.md`
- [ ] Write `modes/survivor-pitch.md`
- [ ] Write `INTOO-CLAUDE.md`
- [ ] Write `.claude/skills/intoo-leadgen/SKILL.md`
- [ ] Create `data/prospect-pipeline.md`
- [ ] Create `templates/states-intoo.yml`

### Phase 3: Smoke Test Lead Gen ⏳ 2026-04-06
- [ ] Run signal scan → find ≥ 3 real signals
- [ ] Qualify top prospect → A–F score
- [ ] Generate outreach (LinkedIn + email)
- [ ] Add to prospect pipeline

### Phase 4: Mira Report ⏳ 2026-04-06
- [ ] Write `reports/INTOO-SMOKE-TEST-REPORT.md`
- [ ] Copy to INTOO workspace folder

---

## References

[1] career-ops README — github.com/eldrgeek/career-ops — Accessed 2026-04-06
[2] INTOO Outplacement — intoo.com/us/solutions/outplacement/ — Accessed 2026-04-06
[3] Challenger Report March 2026 — challengergray.com — Accessed 2026-04-06
[4] Bloomberg Tech Layoffs April 2026 — bloomberg.com — Accessed 2026-04-06
[5] Flywheel Spec Methodology — JE spec-writer pattern — Accessed 2026-04-06
