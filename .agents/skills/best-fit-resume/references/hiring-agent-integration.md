# Hiring-agent integration

This skill **must** use this repository's scoring assets — not a weaker ad-hoc scorer.

## Assets to follow (exact paths)

| Asset | Path | Use |
|-------|------|-----|
| CLI scorer | `score.py` | Primary automated score after each compile |
| Evaluator | `evaluator.py` | Invoked by `score.py` |
| Role config | `roles/<role>/role.json` | Category weights / caps |
| Role criteria | `roles/<role>/criteria.jinja` | Scoring rules + evidence expectations |
| Role system prompt | `roles/<role>/system_message.jinja` | Fairness + mandatory categories |
| Section extractors | `prompts/templates/*.jinja` | Align resume sections with JSON Resume fields the pipeline expects |
| Transform / models | `transform.py`, `models.py` | Understand expected structure (basics, work, projects, skills, awards, profiles) |

Shipped default role: `roles/software_engineering_intern/` (categories: open_source, self_projects, production, technical_skills; max final 120).

## When to run `score.py`

During each of the 3 fit loops, after a PDF exists in `resume-build/output/`:

```bash
python score.py resume-build/output/<name>.pdf --role <role_name>
```

Prerequisites: Python deps (`requirements.txt`), LLM backend per README (Ollama or Gemini), optional `.env`.

### Role selection

1. If user names a role directory under `roles/`, use it.
2. Else if JD matches software intern / early SWE, use `software_engineering_intern`.
3. Else scaffold then customize:

```bash
python score.py --init-role <short_jd_role_slug>
```

Edit `roles/<slug>/{role.json,criteria.jinja,system_message.jinja}` so categories reflect the JD, preserving fairness constraints from the shipped templates. Then score with `--role <slug>`.

## How loop rewrites use the score

From the printed evaluation, prioritize:

1. `areas_for_improvement` (max 3) — turn into concrete resume edits
2. Low category scores + their `evidence` — add real projects, links, production bullets, skills from memory
3. `deductions` — remove tutorial-only fluff framing; add project URLs / demos
4. `bonus_points` opportunities the candidate actually has (portfolio URL, LinkedIn, GSoC, etc.) — ensure those appear in header/sections

Re-read the active role's `criteria.jinja` when deciding what "good" looks like (e.g. true open-source vs personal repos, project complexity, production experience).

## Alignment with extraction prompts

Structure the LaTeX so a later `score.py` PDF parse succeeds:

- Clear **Basics**: name, email, phone, location, profile URLs (GitHub/LinkedIn/portfolio) in header
- **Work** with dates and highlight bullets
- **Education** with institution/dates
- **Skills** as plain keyword lists/categories
- **Projects** with names, highlights, and URLs
- **Awards** when provided

Mirror field intent from `prompts/templates/basics.jinja`, `work.jinja`, `education.jinja`, `skills.jinja`, `projects.jinja`, `awards.jinja`.

## Fallback (no LLM)

If `score.py` fails (no model/API):

1. Manually apply the selected role's `criteria.jinja` checklist to the draft.
2. Produce the same gap fields: category notes, strengths, improvements, deductions risks.
3. Mark report: `Hiring-agent: skipped: <reason>; applied criteria.jinja manually`.

Never replace criteria with a vague "looks good" judgment.
