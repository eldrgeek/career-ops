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
Day 0–3: x1.5 multiplier | Day 4–7: x1.2 | Day 8–30: x1.0 | Day 31–90: x0.7 | Day 91+: x0.3
