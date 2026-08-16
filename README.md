<div align="center">

[中文](./README.zh.md) · **English**

# 🎯 Job Hunt List

**Turn a resume or target direction into a searchable job database.**

Discover · deduplicate · verify · rank · track

[![License](https://img.shields.io/badge/LICENSE-MIT-4c8bf5?style=flat-square&labelColor=333)](./LICENSE)
[![Version](https://img.shields.io/badge/VERSION-1.0.0-2ea44f?style=flat-square&labelColor=333)]()
[![Output](https://img.shields.io/badge/OUTPUT-HTML-f59e0b?style=flat-square&labelColor=333)]()
[![Stars](https://img.shields.io/github/stars/yanliudesign/job-hunt-list?style=flat-square&label=STARS&color=e37f2c&labelColor=333)](https://github.com/yanliudesign/job-hunt-list/stargazers)

[![Claude Code](https://img.shields.io/badge/Claude_Code-Skill-d97757?style=flat-square&labelColor=1a1a1a&logo=anthropic&logoColor=white)](https://claude.ai/code)
[![Codex](https://img.shields.io/badge/Codex-Skill-2ea44f?style=flat-square&labelColor=1a1a1a)]()
[![OpenCode](https://img.shields.io/badge/OpenCode-Skill-4c8bf5?style=flat-square&labelColor=1a1a1a)]()
[![OpenClaw](https://img.shields.io/badge/OpenClaw-Skill-8b5cf6?style=flat-square&labelColor=1a1a1a)]()
[![Hermes](https://img.shields.io/badge/Hermes-Skill-e879a8?style=flat-square&labelColor=1a1a1a)]()

</div>

> 📦 Also included in **[offer-toolkit-skill](https://github.com/yanliudesign/offer-toolkit-skill)** — the full Search · JD · Resume · BQ workflow.

An agent skill for building and maintaining a complete job-hunt list. Give it your resume, target direction, a seed JD, or an existing set of job links. It searches public sources, removes duplicates, separates verified facts from inference, ranks roles against real resume evidence, and generates one self-contained HTML report you can search and filter locally.

Unlike a conventional shortlist, Job Hunt List keeps the complete qualified candidate pool. It is designed for an ongoing search: discover broadly today, deep-review the best roles, and merge new results into the same list next week.

---

## How it works

```text
Resume / target direction / seed JD / existing links
			    ↓
		    Search profile
			    ↓
	  6–12 complementary public queries
			    ↓
	Collect → normalize → deduplicate → verify
			    ↓
	Deep match where the full JD is available
			    ↓
	     Searchable single-file HTML list
```

1. **Build a search profile** — target titles, adjacent titles, level, location, workplace, capabilities, domains, and exclusions.
2. **Run a query matrix** — exact-title, adjacent-title, capability-led, domain-led, scope-led, and company-led searches.
3. **Collect public evidence** — company career pages, accessible LinkedIn pages, and public web results.
4. **Deduplicate** — canonical job ID / URL first; normalized company + title + location as fallback.
5. **Separate fact from inference** — every field follows an explicit evidence state.
6. **Rank without hiding the pool** — deep-score roles with visible JDs; directionally rank the rest.
7. **Generate and validate HTML** — search, filters, domain tags, reasons, gaps, direct links, and consistent row counts.

---

## What you get

The default deliverable is a self-contained file:

```text
job-hunt-list-<candidate-or-topic>-<YYYYMMDD>.html
```

It opens locally with no server, framework, remote font, or build step.

| Report area | What it contains |
|---|---|
| **Search context** | Candidate direction, locations, date range, assumptions, sources, and generation time |
| **Summary** | Unique role count and deep-reviewed count |
| **Search & filters** | Full-text search plus reviewed, priority, AI / Agentic, Principal, and Staff filters when relevant |
| **Job rows** | Role, company, domain tags, fit reason, main gap, date, location, workplace, salary, and match |
| **Evidence note** | Why unreviewed does not mean unqualified and unknown values were not guessed |
| **Search log** | Queries, sources, check time, and any login-wall or rate-limit constraints |

Domain tags sit next to the company and remain searchable. Tier A / Tier B labels are not shown by default; internal priority can still drive ordering and filters.

---

## Evidence before confidence

Job pages are messy: one page exposes salary but not workplace, another exposes a title but hides the JD. This skill evaluates evidence **field by field** instead of treating an accessible URL as proof of everything.

| State | Meaning | Output behavior |
|---|---|---|
| `verified` | Directly visible on a public job or company page | Display the value |
| `partial` | Only a truncated public summary is visible | Note limited evidence when material |
| `inferred` | Derived from title, company, or adjacent signals | Label as directional inference |
| `unknown` | No reliable evidence exists | Leave blank or show `待核验`; never invent |

Unknown salary stays blank. Unknown date, location, or workplace is marked for verification. A verified title never makes the rest of the row automatically verified.

### Deep match formula

Only roles with sufficiently visible JD requirements receive a match range:

```text
Match = 0.60 × Must Have + 0.20 × Nice to Have + 0.20 × Hidden Signal Fit
```

The report shows a range rather than fake decimal precision. Roles without a full JD remain `未深评` and receive a directional reason, not a fabricated match percentage.

Full methodology: [`references/evidence-ranking.md`](references/evidence-ranking.md).

---

## Try it

Use natural language. These all trigger the same workflow:

- *"Build me a job hunt list from my resume."*
- *"Find matching Principal Product Designer roles in the Bay Area or US remote and make a searchable report."*
- *"Use this JD and my resume to find similar AI design jobs."*
- *"Turn these LinkedIn links into a deduplicated job tracker."*
- *"Update last week's list with newly posted roles, but keep first-seen dates."*
- *"LinkedIn is behind a login wall. Continue from public sources and do not guess missing details."*

Start with a resume, target direction, seed JD, collection of links, or a previously generated list. If a missing constraint would materially change the search, the skill asks one question at a time.

---

## Search boundaries

This skill searches and analyzes. It does **not** act as the applicant.

- Never clicks Apply, fills application forms, or sends messages
- Never asks for passwords, verification codes, cookies, or session tokens
- Never bypasses login walls, CAPTCHAs, rate limits, robots restrictions, or access controls
- Never invents salary, posted date, workplace policy, visa policy, job status, or JD requirements
- Never treats temporary inaccessibility as proof that a role is closed

When LinkedIn is unavailable, it switches to accessible company pages, public search results, or generates clickable queries and records the limitation.

---

## Updating an existing list

Job Hunt List is designed to be rerun:

1. Parse existing job IDs and canonical URLs.
2. Add only newly discovered roles.
3. Merge stronger evidence into existing records.
4. Preserve `first_seen` and update `last_checked`.
5. Report how many roles were added, updated, or need review.

It does not mark a role closed solely because the source cannot be reached during one run.

---

## Installation

### Standalone

Clone or download this repository, then place the complete folder in your agent's skills directory:

```bash
git clone https://github.com/yanliudesign/job-hunt-list.git
```

Keep `SKILL.md`, `assets/`, `references/`, and `evals/` together so relative references continue to work.

### With Offer Toolkit

Install **[offer-toolkit-skill](https://github.com/yanliudesign/offer-toolkit-skill)** to connect the full workflow:

```text
Job Hunt List → choose a role
	↓
JD Skill → decode and decide
	↓
Resume Skill → tailor and polish
	↓
BQ Skill → prepare stories and interviews
```

---

## File structure

```text
job-hunt-list/
├── SKILL.md                          # Workflow, boundaries, and output contract
├── README.md                         # English documentation
├── README.zh.md                      # 中文文档
├── LICENSE                           # MIT
├── assets/
│   └── report-spec.md                # HTML data, interaction, and validation spec
├── references/
│   └── evidence-ranking.md           # Evidence states and ranking formula
└── evals/
    └── evals.json                    # Regression scenarios
```

---

## Design principles

1. **Keep the pool, not just the winners.** Today's tentative role may become tomorrow's best fit after its JD is verified.
2. **Evidence is field-specific.** Never let one accessible fact silently validate an entire row.
3. **Freshness affects ordering, not fit.** A new but irrelevant job should not outrank an older strong match.
4. **Unknown is a valid result.** `待核验` is more useful than a polished guess.
5. **The report is an operational tool.** Dense, searchable, and updateable beats a decorative dashboard.

---

## Related skills

- [offer-toolkit-skill](https://github.com/yanliudesign/offer-toolkit-skill) — complete Search · JD · Resume · BQ toolkit
- [job-description-skill](https://github.com/yanliudesign/job-description-skill) — deep-decode one selected role
- [resume-builder-skill](https://github.com/yanliudesign/resume-builder-skill) — tailor and render a print-ready resume
- [Behavior-question-skill](https://github.com/yanliudesign/Behavior-question-skill) — build a story bank and prepare behavioral interviews

---

## License

MIT — fork it, remix it, ship your own version.

Created by [Dreameryanyan](https://www.linkedin.com/in/yanliudesign/) ·
[LinkedIn](https://www.linkedin.com/in/yanliudesign/) ·
[X](https://x.com/yanliudreamer) ·
[Xiaohongshu](https://www.xiaohongshu.com/user/profile/5b2afdf311be104ac3c22931)
