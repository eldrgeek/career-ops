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

## Mode File Mapping

| Mode Argument | File |
|---------------|------|
| signal-scan | `modes/signal-scan.md` |
| qualify | `modes/qualify.md` |
| outreach | `modes/outreach.md` |
| research | `modes/account-research.md` |
| survivor | `modes/survivor-pitch.md` |

## Pipeline Mode

When mode is `pipeline`, read `data/prospect-pipeline.md` and display:
1. Total prospects count
2. Breakdown by Stage
3. Top 5 highest-scored prospects
4. Any prospects with days-since-signal < 7 (urgent)
5. Suggest next actions for each stage

## Discovery Mode (no args)

Show:
```
intoo-leadgen — INTOO Lead Gen Command Center

  /intoo-leadgen signal-scan        -> Scan for new layoff signals
  /intoo-leadgen qualify {company}  -> A-F prospect scoring
  /intoo-leadgen outreach {company} -> Generate LinkedIn + email outreach
  /intoo-leadgen research {company} -> Deep account research
  /intoo-leadgen survivor {company} -> Post-layoff survivor upsell pitch
  /intoo-leadgen pipeline           -> View prospect pipeline

Files: data/prospect-pipeline.md | reports/ | intoo-profile.yml
```
