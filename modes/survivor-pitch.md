# Mode: survivor-pitch — Post-Layoff Survivor Upsell

Generate follow-up outreach targeting the same CHRO/VP HR, 30–90 days after the initial layoff event, focused on retaining and re-engaging the survivors.

## Context

After a layoff, the CHRO's attention shifts:
- **Days 0–30:** Managing the transition (outplacement, severance, PR)
- **Days 30–90:** Retaining the survivors (morale, engagement, productivity)
- **Days 90+:** Rebuilding (hiring, culture reset, employer brand repair)

This mode targets the Day 30–90 window — the CHRO is now worried about the people who STAYED.

## Prerequisites

Before generating survivor pitch, read:
1. `intoo-profile.yml` — INTOO career development and mobility value props
2. The original prospect report from `reports/` (the initial layoff evaluation)
3. `data/prospect-pipeline.md` — current prospect status and signal date
4. Calculate: `days_since_signal = today - signal_date`

**Timing check:**
- If days_since_signal < 30: "Too early for survivor pitch. The CHRO is still managing the transition. Wait until Day 30+."
- If days_since_signal > 120: "Late for survivor pitch. Consider a fresh angle (employer brand rebuild, new fiscal year planning)."
- If 30–90: Proceed with standard survivor pitch.
- If 91–120: Proceed but adjust framing to "rebuilding" rather than "surviving."

## Survivor Value Props

These are DIFFERENT from the outplacement pitch. INTOO's career development services include:
- **Career coaching for retained employees** — help survivors process change and re-engage
- **Internal mobility platform** — help companies redeploy talent instead of losing them
- **Manager training** — equip managers to lead through post-layoff uncertainty
- **Skills assessment and development** — identify gaps in the remaining workforce
- **Engagement surveys** — measure survivor sentiment and identify flight risks

Key stats to reference:
- "After a layoff, 74% of remaining employees report decreased productivity" (cite if available)
- "Voluntary turnover spikes 31% in the 6 months following a layoff"
- "Companies that invest in survivor support see 2x faster recovery in engagement scores"
(Mira: confirm or replace these stats with real INTOO data)

## Output: Follow-Up Outreach

### Follow-Up Email

**Subject options (pick best fit):**
- `{Company} — supporting the team that stayed`
- `Post-transition: keeping momentum at {Company}`
- `{Company} next chapter — quick thought`

**Body — 3 paragraphs:**

**P1 — Acknowledge the transition (2 sentences):**
Reference the original layoff (tactfully). Acknowledge they've been through a difficult period. Do NOT say "hope you're doing well" — they're exhausted.

**P2 — Pivot to survivors (2-3 sentences):**
The people who stayed are watching. They're anxious about their own futures. The best companies invest in their development NOW — it signals stability and commitment.

**P3 — Specific offer + ask (2 sentences):**
INTOO's career development platform / coaching / internal mobility. Low-friction CTA (demo, case study, quick call).

### Follow-Up LinkedIn Message

Shorter version of the email (under 500 chars). Same Hook-Pivot-Ask structure.

## Output File

Write to: `reports/{num}-{company-slug}-{date}-survivor.md`

```markdown
# Survivor Pitch: {Company} — {date}
**Original Signal:** {date and description}
**Days Since Signal:** {N}
**Original Score:** {X.X}/5
**Target Contact:** {name, title}

---

## Follow-Up Email
**Subject:** {subject}

{email body}

## Follow-Up LinkedIn Message ({N} chars)
> {message}

---

**Status:** Draft — requires human review before sending.
**Context:** This is a SECOND touch, following up on the original outplacement outreach.
```

## Post-Generation

1. Update `data/prospect-pipeline.md` — add note: "Survivor Follow-Up Ready ({date})"
2. Do NOT change the Stage — this is an upsell on the same prospect
3. Print reminder: "This is a follow-up on an existing prospect. Review both the original outreach and this survivor pitch before sending."

## Rules

- NEVER generate survivor pitch if no original outreach exists (run outreach first)
- NEVER be tone-deaf about the layoff — always acknowledge the difficulty
- NEVER promise specific outcomes (e.g., "we guarantee retention") — use data and case studies
- The angle is DIFFERENT from outplacement: this is about development, mobility, and engagement — not transition
- If case studies are "TBD" in intoo-profile.yml, use the general survivor stats above
