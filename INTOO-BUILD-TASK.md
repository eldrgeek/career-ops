# INTOO Lead Gen Build Task
**For:** Claude Code running with `--dangerously-skip-permissions`
**Workdir:** ~/Projects/career-ops
**Context:** Read INTOO-LEADGEN-SPEC.md for full specification before starting.

---

## YOUR JOB

You are building the INTOO Lead Generation System by adapting career-ops. Work through the 4 phases below IN ORDER. Do not skip phases. At the end, write a Mira Report summarizing everything.

Read `INTOO-LEADGEN-SPEC.md` first. It has all data models, file structures, scoring rubrics, and success criteria.

---

## PHASE 1: Smoke Test career-ops (verify baseline works)

**Goal:** Confirm the existing career-ops system works before we build on top of it.

**Steps:**

1. Check if `config/profile.yml` exists. If not, copy from `config/profile.example.yml` and fill with placeholder data:
   ```yaml
   candidate:
     full_name: "Test User"
     email: "test@example.com"
     location: "San Francisco, CA"
   target_roles:
     primary: ["Senior Engineer"]
   ```

2. Check if `cv.md` exists. If not, create a minimal stub:
   ```markdown
   # Test User — CV
   ## Summary
   Senior software engineer with 10 years experience in Python, cloud architecture, and AI systems.
   ## Experience
   - Senior Engineer, Acme Corp (2020–2026): Led backend platform team, 5 engineers.
   ## Education
   - BS Computer Science, State University, 2014
   ## Skills
   Python, TypeScript, AWS, LLMs, system design
   ```

3. Check if `modes/_profile.md` exists. If not, copy from `modes/_profile.template.md`.

4. Run the career-ops evaluation with this sample JD text (use `claude -p` with the mode files):
   ```
   Senior AI Engineer at CloudTech Inc.
   We're looking for a Senior AI Engineer to join our platform team.
   Requirements: 5+ years Python, experience with LLMs and vector databases, 
   strong system design skills. Remote. $180k–$220k base.
   ```

   To run the evaluation, read `modes/_shared.md` and `modes/oferta.md`, then evaluate the JD against the cv.md and profile.

5. Write the evaluation report to `reports/001-cloudtech-smoke-test-2026-04-06.md`

6. Try to generate a PDF: `node generate-pdf.mjs reports/001-cloudtech-smoke-test-2026-04-06.md 2>&1`

7. Record Phase 1 result: PASS (report created) or FAIL (with error details).

---

## PHASE 2: Build All New Modules

Create ALL of these files. The spec in INTOO-LEADGEN-SPEC.md has the detailed content for each.

### 2.1 — INTOO-CLAUDE.md
```markdown
# INTOO Lead Generation System — Agent Context

## What This Is
An adaptation of career-ops (github.com/santifer/career-ops) for B2B outplacement lead generation. 
Instead of a job-seeker scanning job boards, this is INTOO scanning for companies experiencing 
layoff events that make them buyers of outplacement services.

## Architecture Map
| Original | Adapted |
|----------|---------|
| portals.yml | trigger-sources.yml |
| cv.md + profile.yml | intoo-profile.yml |
| modes/scan.md | modes/signal-scan.md |
| modes/oferta.md | modes/qualify.md |
| modes/contacto.md | modes/outreach.md |
| modes/deep.md | modes/account-research.md |
| data/applications.md | data/prospect-pipeline.md |

## Skill Commands
| Command | What It Does |
|---------|-------------|
| /intoo-leadgen signal-scan | Scan trigger-sources for new layoff signals |
| /intoo-leadgen qualify {company} | Score a prospect company A–F |
| /intoo-leadgen outreach {company} | Generate LinkedIn + email outreach |
| /intoo-leadgen research {company} | Deep account research |
| /intoo-leadgen survivor {company} | Generate survivor upsell pitch |
| /intoo-leadgen pipeline | View prospect pipeline |

## Key Files
- `intoo-profile.yml` — INTOO's ICP, value props, case studies (like cv.md)
- `trigger-sources.yml` — Signal sources to scan (like portals.yml)
- `data/prospect-pipeline.md` — Master prospect tracker
- `reports/` — One report per qualified prospect

## Safety Rules
1. NEVER auto-send any outreach. All output is drafts for human review.
2. NEVER generate outreach for prospects scoring < 3.5
3. NEVER scrape LinkedIn profiles — use WebSearch for contact info only
4. ALWAYS cite sources in every prospect report
5. Human decision required before any prospect advances to "Contacted" stage

## Urgency Decay
Day 0–3: ×1.5 multiplier | Day 4–7: ×1.2 | Day 8–30: ×1.0 | Day 31–90: ×0.7 | Day 91+: ×0.3
```

### 2.2 — intoo-profile.yml
Use the full YAML structure from INTOO-LEADGEN-SPEC.md section "intoo-profile.yml Structure".
Save to `intoo-profile.yml` in project root.
Note in comments: "Mira: fill in real case studies and confirm ICP parameters."

### 2.3 — trigger-sources.yml
Create with these signal sources (at minimum):
```yaml
# INTOO Lead Gen — Signal Sources
# Like portals.yml for career-ops, but for layoff/restructuring signals

scan_history: "data/signal-history.tsv"

signal_sources:

  # ── REAL-TIME TRACKERS ──────────────────────────────
  - name: layoffs_fyi
    type: websearch
    query: "site:layoffs.fyi 2026"
    enabled: true
    priority: high
    notes: "Primary real-time tracker. Check weekly."

  - name: trueup_layoffs
    type: websearch
    query: "site:trueup.io/layoffs 2026"
    enabled: true
    priority: high

  # ── NEWS SIGNALS ────────────────────────────────────
  - name: tech_layoffs_recent
    type: websearch
    query: "tech company layoffs April 2026"
    enabled: true
    priority: high

  - name: restructuring_news
    type: websearch
    query: '"workforce reduction" OR "restructuring" OR "right-sizing" site:businesswire.com OR site:prnewswire.com 2026'
    enabled: true
    priority: medium

  - name: crunchbase_layoffs
    type: websearch
    query: "crunchbase.com layoffs 2026 startup"
    enabled: true
    priority: medium

  # ── FINANCIAL SIGNALS (PRE-LAYOFF) ──────────────────
  - name: sec_8k_workforce
    type: websearch
    query: 'site:sec.gov 8-K "workforce reduction" OR "headcount reduction" 2026'
    enabled: true
    priority: high
    notes: "Earliest signal — appears before press releases"

  - name: earnings_restructuring
    type: websearch
    query: '"workforce optimization" OR "organizational redesign" earnings call Q1 2026'
    enabled: true
    priority: medium

  # ── M&A SIGNALS (PREDICTIVE) ────────────────────────
  - name: ma_announcements
    type: websearch
    query: "merger acquisition announced 2025 2026 tech finance workforce integration"
    enabled: false
    priority: low
    notes: "M&A → redundancy layoffs typically 6-12 months later. Enable for 60-day preview."

  # ── COMPETITOR DISPLACEMENT ──────────────────────────
  - name: competitor_reviews_negative
    type: websearch
    query: '"Lee Hecht Harrison" OR "Right Management" OR "Challenger Gray" negative review 2025 2026 site:g2.com OR site:gartner.com'
    enabled: false
    priority: low
    notes: "Unhappy competitor customers. Enable for displacement campaigns."

icp_filter:
  min_employees: 500
  priority_verticals: [tech, finance, healthcare, manufacturing, retail, professional_services]
  exclude_patterns: ["seed stage", "solo founder", "non-profit", "government", "startup <50"]
  geography_primary: ["United States", "Canada", "United Kingdom", "Australia"]
```

### 2.4 — modes/signal-scan.md
Write a comprehensive mode file that instructs Claude Code to:
1. Read `trigger-sources.yml` for scan configuration
2. Read `data/signal-history.tsv` to avoid duplicates
3. Execute WebSearch for each enabled source
4. Extract: company name, approximate employee count, layoff scale, announcement date, news URL
5. Filter by ICP (`icp_filter` from trigger-sources.yml)
6. Deduplicate against signal-history.tsv AND prospect-pipeline.md
7. For each new signal: add to `data/prospect-pipeline.md` with Stage: "Signal"
8. Record in `data/signal-history.tsv`: `{url}\t{date}\t{source}\t{company}\t{scale}\t{added|skipped}`
9. Print summary: N signals found, N new, N duplicates, N below ICP threshold

### 2.5 — modes/qualify.md
Write a mode file for prospect qualification using the 6-block A–F scoring from the spec.

Blocks:
- A: Urgency Signal (30% weight) — How recent? Apply urgency decay multiplier.
- B: ICP Fit (25%) — Employee count, vertical, geography vs. intoo-profile.yml
- C: Deal Size Estimate (20%) — Layoff headcount × estimated per-seat value ($1,500–$5,000)
- D: Decision-Maker Access (15%) — WebSearch for CHRO/VP HR name and LinkedIn presence
- E: Competitive Position (10%) — Is there a known incumbent? Any displacement signals?
- F: Engagement Plan (narrative) — Recommended first touch, angle, timing

Score thresholds: 4.5+ Hot, 4.0–4.4 Warm, 3.5–3.9 Qualified, <3.5 Pass.

Output: Report at `reports/{num}-{company-slug}-{date}.md` following the header format in the spec.

Rule: If score < 3.5, write report with reason but DO NOT generate outreach. Log as "Pass" in pipeline.

### 2.6 — modes/outreach.md
Write a mode file that generates outreach for a qualified prospect (score ≥ 3.5).

Reads: `intoo-profile.yml` (value props, case studies) + prospect report from `reports/`

Generates three outputs:
1. **LinkedIn connection request** — ≤ 300 characters. Framework: Hook (their specific situation) + Proof (one INTOO stat) + Ask (15-min call). No corporate speak. No "I'm passionate about..."
2. **LinkedIn InMail** — Longer version, ≤ 1,000 chars. Same structure + one relevant case study.
3. **Cold email** — Subject line + 3-paragraph body. Subject: "{Company} team transition — quick note". 
   - P1: Specific hook (reference the announcement)
   - P2: Proof (case study most relevant to their vertical/size)
   - P3: Low-friction ask (demo or Calendly link placeholder)

Rules:
- NEVER send automatically. All output written to `reports/{num}-{company-slug}-{date}-outreach.md`
- Always reference the specific signal that triggered outreach
- Always use a real INTOO stat from `intoo-profile.yml`
- If case studies are "TBD", use the 2.5× faster stat as primary proof
- Update prospect Stage to "Outreach Ready" in `data/prospect-pipeline.md`

### 2.7 — modes/account-research.md
Write a mode file for deep account research. Mirrors `modes/deep.md` but for B2B outplacement context.

Six research axes:
1. **Layoff Details** — Scale, which departments, public reasons given, timeline
2. **HR Leadership** — CHRO/VP HR name, LinkedIn URL, tenure, prior company (did they use outplacement there?)
3. **Vendor Intelligence** — Any public mention of Lee Hecht Harrison, Right Management, etc.? Check SHRM vendor reviews.
4. **Employer Brand Health** — Glassdoor score + trend, Indeed rating, Blind posts
5. **Financial Health** — Can they afford outplacement? Recent funding, revenue, layoff as cost-cut vs. strategic
6. **INTOO Angle** — Which value prop resonates most? Which case study to lead with? Ideal hook for outreach.

### 2.8 — modes/survivor-pitch.md
Write a mode file for the survivor upsell sequence.

Context: 30–90 days after a layoff, the CHRO's attention shifts to retaining and re-engaging the survivors. This is a second sales opportunity from the same event.

Mode reads: The original prospect report + current date to calculate days-since-signal.

Generates:
1. A follow-up LinkedIn message or email (different angle from initial outreach)
2. Framing: "You navigated the transition. Now — how do you keep the people who stayed?"
3. References INTOO's career development and mobility services
4. Flags in pipeline: add "Survivor Follow-Up Sent" note

### 2.9 — data/prospect-pipeline.md
Create the empty tracker:
```markdown
# INTOO Prospect Pipeline

| # | Signal Date | Company | Scale | Vertical | Score | Stage | Contact | Report | Notes |
|---|-------------|---------|-------|----------|-------|-------|---------|--------|-------|
```

### 2.10 — data/signal-history.tsv
Create the empty history file:
```
url	signal_date	source	company	scale	status
```

### 2.11 — templates/states-intoo.yml
Use the pipeline states from the spec.

### 2.12 — .claude/skills/intoo-leadgen/SKILL.md
```yaml
---
name: intoo-leadgen
description: INTOO outplacement lead generation system — scan for layoff signals, qualify prospects, generate SDR outreach
user_invocable: true
args: mode
---

# intoo-leadgen — Router

## Mode Routing

| Input | Mode |
|-------|------|
| (empty) | Show command menu |
| signal-scan | Run signal scanner |
| qualify {company} | Qualify a specific company |
| outreach {company} | Generate SDR outreach |
| research {company} | Deep account research |
| survivor {company} | Survivor upsell pitch |
| pipeline | Show prospect pipeline status |

## Context Loading

For all modes except pipeline:
- Read `INTOO-CLAUDE.md` for system context
- Read `intoo-profile.yml` for INTOO ICP + value props
- Read `trigger-sources.yml` for scan configuration

Then read the specific mode file: `modes/{mode-mapped-filename}.md`

## Discovery Mode (no args)
Show:
```
intoo-leadgen — INTOO Lead Gen Command Center

  /intoo-leadgen signal-scan        → Scan for new layoff signals
  /intoo-leadgen qualify {company}  → A–F prospect scoring
  /intoo-leadgen outreach {company} → Generate LinkedIn + email outreach
  /intoo-leadgen research {company} → Deep account research
  /intoo-leadgen survivor {company} → Post-layoff survivor upsell pitch
  /intoo-leadgen pipeline           → View prospect pipeline

Files: data/prospect-pipeline.md | reports/ | intoo-profile.yml
```
```

---

## PHASE 3: Smoke Test the Lead Gen System

### 3.1 — Signal Scan
Read `modes/signal-scan.md` and `trigger-sources.yml`, then execute a real scan.

Use WebSearch to find at least 3 real recent layoff events (April 2026). Extract company name, scale, date, source URL for each.

Add all to `data/prospect-pipeline.md` with Stage: "Signal".
Record in `data/signal-history.tsv`.

### 3.2 — Qualify Top Prospect
Pick the most promising signal (largest company, most recent, tech vertical preferred).

Read `modes/qualify.md` and `intoo-profile.yml`.

Run the 6-block qualification. Use WebSearch to find:
- Actual company employee count (verify the layoff scale)
- CHRO or VP HR name and LinkedIn presence
- Any known outplacement vendor
- Glassdoor score

Write full qualification report to `reports/001-{company-slug}-2026-04-06.md`.

### 3.3 — Generate Outreach
If the score is ≥ 3.5:
- Read `modes/outreach.md`
- Generate all 3 outreach formats (LinkedIn request, InMail, cold email)
- Write to `reports/001-{company-slug}-2026-04-06-outreach.md`
- Update Stage in `data/prospect-pipeline.md` to "Qualified"

If score < 3.5: log result, note why, continue to Phase 4.

---

## PHASE 4: Write the Mira Report

Write `reports/INTOO-SMOKE-TEST-REPORT.md` with this structure:

```markdown
# INTOO Lead Gen System — Smoke Test Report
**Date:** 2026-04-06
**System Version:** 0.1.0

## Executive Summary
[2-3 sentences: what was built, did it work, what's next]

## Phase 1: career-ops Baseline
**Result:** PASS / FAIL
[Brief notes on what worked or failed]

## Phase 2: Modules Built
[List of all files created, one line each]

## Phase 3: Live Smoke Test Results
### Signals Found
[List of companies found with source URLs]

### Top Prospect: {Company Name}
**Score:** X.X/5
**Urgency:** Day N post-announcement
**Key factors:** [2-3 bullet points]

### Outreach Generated (sample)
**LinkedIn (N chars):**
> {the generated message}

**Email Subject:** {subject}
**Email Preview:** {first paragraph}

## What's Working
[Honest assessment]

## What Needs Tuning
[Specific gaps or issues found]

## What Mira Must Provide
1. **Real case studies** (2–3): Company size, industry, employees transitioned, weeks to placement, outcome. Currently all case studies in intoo-profile.yml are "TBD".
2. **ICP confirmation**: Verify the 500–10,000 employee range and vertical priorities are correct.
3. **Deal value estimates**: What is the actual per-seat or per-event contract range? Used for deal size scoring.
4. **Competitor displacement angles**: Any customers won from Lee Hecht Harrison, Right Management, etc.?
5. **SDR email templates**: Current approved templates from sdr-email-reference.html should be mapped into outreach.md.
6. **Demo/Calendly link**: The outreach emails currently use a placeholder. Replace with real booking link.

## Next Steps
1. Mira fills in intoo-profile.yml with real case studies and confirms ICP
2. Run full signal scan covering last 30 days of layoff news
3. Qualify top 10 prospects and review scores with Mira to calibrate rubric
4. v0.2: integrate with dashboard TUI (relabel columns for prospect pipeline)
5. v0.2: add scheduled scan (weekly auto-run via schedule skill)
```

After writing the Mira Report:
- Copy it to `/Users/mikewolf/Projects/yeshie/../INTOO/INTOO-SMOKE-TEST-REPORT.md` if that path works,
  OR write a copy to `~/Desktop/INTOO-SMOKE-TEST-REPORT.md` so Mike can find it.

---

## COMPLETION CHECKLIST

Before ending your session, verify:
- [ ] Phase 1: career-ops smoke test result recorded (PASS/FAIL)
- [ ] Phase 2: All 12 new files created
- [ ] Phase 3: ≥ 3 real signals found and logged in prospect-pipeline.md
- [ ] Phase 3: ≥ 1 prospect fully qualified with report
- [ ] Phase 3: Outreach generated (if score ≥ 3.5)
- [ ] Phase 4: Mira Report written
- [ ] Mira Report copied to accessible location

If any phase fails, record the failure in the Mira Report and continue to next phase. 
Do not stop for permission prompts — you have --dangerously-skip-permissions.
