# ATS / JD fit scoring (0–100)

Use this rubric every loop. **Target ≥ 90** before finalize (or best effort after 3 loops).

Do not invent experience to chase points. Only score what the resume actually contains from user memory / answers.

## Score components

| Component | Max | What to check |
|-----------|-----|----------------|
| Keyword coverage | 30 | Must-have JD terms appear naturally in skills + bullets; nice-to-haves where honest |
| Role clarity | 15 | Clear titles, scope, ownership; work defined concretely |
| Evidence / metrics | 15 | Quantified outcomes; tools named; verifiable links on projects |
| ATS parseability | 15 | Standard headings, plain text, no icon/textbox layout traps; consistent dates |
| Section completeness | 10 | All user-filled sections present; header links (GitHub, LinkedIn, others) |
| Alignment / ordering | 10 | Strongest JD-relevant experience and projects first; density fits 2 pages |
| Human voice | 5 | Specific, non-generic; no banned AI watermarks (see humanize.md) |

**ATS total** = sum of components (0–100).

## Keyword procedure (each loop)

1. List JD must-haves and nice-to-haves as exact phrases.
2. Mark each: present / partial / missing on the current resume draft.
3. Add missing terms only where the candidate's real experience supports them.
4. Re-scan JD after rewrite; update the list.
5. Penalize stuffing (keyword repeated without context) under Keyword coverage and Human voice.

## Gap analysis output (required each loop)

```
ATS: <n>/100
Missing must-haves: ...
Weak evidence: ...
Parse risks: ...
Rewrite plan: ...
```

## Relationship to hiring-agent scores

This 0–100 ATS/JD score is the **primary** loop gate (90+).

Separately run this repo's hiring-agent evaluator (`score.py` / role `criteria.jinja`) and use its `areas_for_improvement` and category evidence to drive rewrites. Report both scores; do not replace the hiring-agent rubric with a homemade one.
