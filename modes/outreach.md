# Mode: outreach — SDR Outreach Generator

Generate personalized outreach messages for a qualified prospect (score >= 3.5).

## Prerequisites

Before generating outreach, read:
1. `intoo-profile.yml` — INTOO value props, case studies, outreach defaults
2. The prospect's qualification report from `reports/`
3. `data/prospect-pipeline.md` — current prospect status

**Gate check:** If the prospect's score is < 3.5, REFUSE to generate outreach. Explain why and suggest re-qualifying or marking as Pass.

## Output: Three Outreach Formats

### 1. LinkedIn Connection Request (max 300 characters)

**Framework:** Hook + Proof + Ask

- **Hook:** Reference their SPECIFIC situation (the layoff announcement, the restructuring, the filing). Never generic.
- **Proof:** One INTOO stat relevant to their pain. Use from `intoo-profile.yml`:
  - "2.5x faster placement vs. industry average"
  - "Instant live coach access — 7 days/week"
  - "130+ countries, 2,100+ coaches"
  - Or a case study stat if available
- **Ask:** 15-minute call. Low friction. No commitment.

**Rules:**
- Must be under 300 characters
- No corporate speak ("I'm passionate about...", "leveraging synergies...")
- No "I noticed you..." (overused)
- Lead with THEIR situation, not INTOO's pitch
- Sound like a human SDR, not a template

**Example structure:**
```
Saw the {Company} announcement this week. We helped a similar {vertical} co transition {N} people — 2.5x faster than avg. Worth a 15-min call? Happy to share what worked.
```

### 2. LinkedIn InMail (max 1,000 characters)

**Same framework as connection request, expanded:**
- Hook: 2-3 sentences about their specific situation
- Proof: One relevant case study (or headline stat if case studies are TBD)
- Context: Brief INTOO positioning (1 sentence)
- Ask: 15-minute call with specific value promise ("I can walk you through how {similar_company} handled this")

**Rules:**
- Must be under 1,000 characters
- Reference the specific signal that triggered this outreach
- If case studies in `intoo-profile.yml` are "TBD", use the 2.5x stat as primary proof
- Include a concrete reason for the call (not just "let's chat")

### 3. Cold Email

**Subject line:** `{Company} team transition — quick note`

**Body — 3 paragraphs max:**

**P1 — Hook (2-3 sentences):**
Reference the specific announcement. Show you know what's happening. Be respectful — layoffs are sensitive. No "Congrats on the restructuring!"

**P2 — Proof (2-3 sentences):**
Most relevant case study from `intoo-profile.yml`. Match by vertical and company size. Include specific numbers (employees transitioned, time to placement, satisfaction score). If case studies are TBD, use:
> "Our clients see 2.5x faster placement rates, with instant access to 2,100+ coaches across 130 countries — no chatbots, no wait."

**P3 — Ask (1-2 sentences):**
Low-friction CTA. Options:
- Calendly link (from `intoo-profile.yml` outreach_defaults)
- "Would a 15-minute walkthrough be useful this week?"
- "Happy to send a quick overview deck — would that be helpful?"

**Signature:**
```
{SDR name — placeholder}
INTOO | intoo.com
```

## Output File

Write all three formats to: `reports/{num}-{company-slug}-{date}-outreach.md`

```markdown
# Outreach: {Company} — {date}
**Prospect Score:** {X.X}/5
**Target Contact:** {name, title}
**Signal:** {brief description}
**Days Since Signal:** {N}

---

## LinkedIn Connection Request ({N} chars)
> {message}

## LinkedIn InMail ({N} chars)
> {message}

## Cold Email
**Subject:** {subject}

{email body}

---

**Status:** Draft — requires human review before sending.
**Next step:** SDR reviews, personalizes, and sends via their own LinkedIn/email.
```

## Post-Outreach Generation

1. Update `data/prospect-pipeline.md` — set Stage to "Outreach Ready"
2. Add note with outreach file path
3. Print reminder: "Outreach is a DRAFT. Human review required before sending."

## Rules

- NEVER auto-send anything. All output is written to files only.
- NEVER use the prospect's personal information beyond name and title.
- NEVER reference internal INTOO pricing in outreach messages.
- ALWAYS reference the specific signal/announcement — generic outreach is worthless.
- ALWAYS use a real INTOO stat from `intoo-profile.yml`.
- If the contact name is unknown, address the email to "Head of HR" and note that contact research is needed.
- Keep tone empathetic — layoffs are difficult for everyone involved. Lead with help, not sales.
