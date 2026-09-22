---
name: best-fit-resume
description: >-
  Builds a best-fit 2-page LaTeX resume, scores it against a job description,
  researches the role/company and ATS practices online (≤3 searches / 2 reads),
  iterates 3 times to 90+ ATS fit, humanizes wording, compiles PDF to
  resume-build/output/, archives under resume-archive/pdf/, and logs
  Name/Description/Yes-No/Date/Time/Role/Company/PDF/ATS in
  resume-tracker/resumes.xlsx. Uses this repo's hiring-agent scorers and rubrics.
  Use when building, tailoring, or optimizing a resume; updating resume memory;
  archiving a PDF; or logging Yes/No decisions in the tracker.
compatibility: Cursor, OpenCode, Antigravity, Claude Code, Codex, Hermes
---

# Best-Fit Resume

Build a tailored 2-page LaTeX resume, score it against a JD, iterate for fit, humanize copy, and compile PDF to a folder separate from source.

**Harness-agnostic:** use only file read/write and shell (`pdflatex` / `latexmk`, `python score.py`). No Cursor-only tools.

## When to use

- User wants a resume built, tailored, or optimized for a job description
- User says "update my resume memory" / similar
- User wants a LaTeX → PDF resume with ATS scoring

## Paths (project root = this hiring-agent repo)

| What | Path |
|------|------|
| Canonical skill | `.agents/skills/best-fit-resume/SKILL.md` |
| Memory store | `.resume-memory/profile.json` |
| Memory schema | `.resume-memory/profile.schema.json` |
| TeX source | `resume-build/src/` |
| Build PDF | `resume-build/output/` (compile folder; never mix with `.tex`) |
| Saved PDFs | `resume-archive/pdf/` (finished copies only) |
| Excel tracker | `resume-tracker/resumes.xlsx` via `resume-tracker/track_resume.py` |
| Built-in formats | `.agents/skills/best-fit-resume/assets/formats/` (`classic`, `compact`, `skills-first`, `academic`, `projects-first`) |
| Format index | `.agents/skills/best-fit-resume/references/formats.md` |
| Hiring scorer | `score.py` |
| Role rubrics | `roles/<role>/` (`role.json`, `criteria.jinja`, `system_message.jinja`) |
| Extraction prompts | `prompts/templates/*.jinja` |

Read references only when needed:

- [questions.md](references/questions.md) — upfront intake list
- [ats-jd-scoring.md](references/ats-jd-scoring.md) — 0–100 ATS/JD rubric
- [humanize.md](references/humanize.md) — voice + banned AI phrases
- [hiring-agent-integration.md](references/hiring-agent-integration.md) — `score.py` loop
- [memory.md](references/memory.md) — load / merge / update memory
- [tracking.md](references/tracking.md) — archive PDF + Excel Yes/No log
- [formats.md](references/formats.md) — built-in layout names and when to use each

---

## Strict conversation order

Do **not** reorder. Do **not** drip questions later except for the explicit Step 4 additions pass.

### Step 1 — Intake only (first message)

1. Load `.resume-memory/profile.json` if it exists (see [memory.md](references/memory.md)).
2. If memory is complete: confirm the profile in a short summary and ask **only** what is missing or what they want to change.
3. If memory is missing/incomplete: ask **ALL** questions from [questions.md](references/questions.md) in **one** message. Wait for answers before Step 2.
4. After answers: merge into `.resume-memory/profile.json`. **Never drop** previously filled fields.

### Step 2 — Template

1. Ask which format they want. Offer the built-in names in one line: **classic**, **compact**, **skills-first**, **academic**, **projects-first**. Also accept a path or a pasted template. Details: [formats.md](references/formats.md).
2. If they name a built-in: copy that file from `.agents/skills/best-fit-resume/assets/formats/<name>.tex` and follow its section order, type size, and spacing. Keep every filled profile section even if you move it.
3. If they paste or point at their own template: analyze structure (sections, order, density, fonts, margins) and follow it.
4. If they say default, or do not choose: use `assets/formats/classic.tex`.

### Step 3 — Job description + research + 3-loop fit cycle

1. Ask for the full job description (paste or file path). Capture **target role** and **target company** (ask once here if missing from the JD or memory — do not reopen the full intake).
2. Analyze JD: role title, must-haves, nice-to-haves, tools, domain terms.
3. Extract keywords (exact phrases where natural).
4. **Web research (before Loop 1)** — see [Web research](#web-research-before-fit-loops) below. Produce a short keyword/scoring note. Do not invent experience.
5. Run the **keyword/fit loop exactly 3 times** (see below), feeding the research note into every loop. Target **ATS score ≥ 90** on the rubric in [ats-jd-scoring.md](references/ats-jd-scoring.md).

### Step 4 — Additions, then finalize

1. Ask if they want any additions or final tweaks.
2. Apply additions, re-humanize, then write LaTeX + compile + report.

### Step 5 — Write, compile, archive, track, report

1. Write `.tex` under `resume-build/src/`.
2. Compile PDF into `resume-build/output/` only.
3. Verify PDF page count is **2** (compile and check; tighten or expand if not).
4. Archive and log with one command (below). Do not copy the PDF by hand and do not build the spreadsheet in chat.
5. Report: final ATS score, hiring-agent score (if run), loop deltas, archive path, tracker row.

---

## Web research (before fit loops)

Run **after** the JD is in hand and **before** Loop 1. Do not drip new profile questions. Research informs honest keyword choice, section order, and bullet shape — it does **not** replace the 3-loop cycle or `score.py`.

1. Search the public web for the target **role + company**: title variants, skills that this posting and similar public listings emphasize, and recent ATS scoring practices (keyword placement, standard headings, measurable bullets, parseable layout).
2. Use whatever web search/fetch tools the current harness provides (OpenCode, Antigravity, Cursor, Claude, Codex, etc.). Prefer harness-agnostic search/fetch. **Do not require a Cursor-only tool.** If the harness has no web tool: say so in one line, then continue from the JD and any pages the user pasted.
3. **Token budget:** at most **3 searches** and **2 page reads**. From those, write a short private note (must-have terms, phrasing patterns, layout rules). Do **not** paste articles into the chat or into the resume.
4. Use the note only for: keywords the candidate can honestly claim (from memory), section order, and bullet shape. **Never invent** employers, metrics, tools, or dates the user did not provide.
5. Feed the note into each of the 3 loops (keyword pass + gap rewrite). Final report includes one line: what was checked online (**query topics only**, not full page text).

---

## 3-loop JD scoring (mandatory)

Each loop = **score → gap analysis → rewrite → re-score**.

Copy and track:

```
Fit loop progress:
- [ ] Loop 1: score, gaps, rewrite, re-score
- [ ] Loop 2: score, gaps, rewrite, re-score
- [ ] Loop 3: score, gaps, rewrite, re-score
```

### Per loop — apply in this order

1. **Keyword pass**  
   Mirror JD + research-note keywords naturally into skills, experience bullets, and project blurbs. No stuffing. Re-read JD after edits; add any still-missing high-value terms that the candidate can honestly claim (from memory only — never invent experience).

2. **ATS / JD fit score (0–100)**  
   Score with [ats-jd-scoring.md](references/ats-jd-scoring.md). Record numeric score + top gaps.

3. **Hiring-agent score (this repo)**  
   Follow [hiring-agent-integration.md](references/hiring-agent-integration.md):
   - Prefer: compile PDF → `python score.py <pdf> --role <role>`
   - Role: user-specified, or closest under `roles/`, or scaffold with `python score.py --init-role <name>` then edit rubric for the JD.
   - Use `areas_for_improvement` and category evidence from the evaluator as rewrite input.
   - Apply fairness + scoring rules from that role's `criteria.jinja` and `system_message.jinja` (do not invent a weaker scorer).
   - If LLM/`score.py` is unavailable: apply the same role criteria manually as a structured gap list; note fallback in the report.

4. **Humanize**  
   Apply [humanize.md](references/humanize.md) after each rewrite.

5. **Rewrite**  
   Close gaps: stronger metrics, clearer scope, missing keywords, links, production/project evidence per rubric. Keep every user-filled section unless the template forbids it.

After loop 3: if ATS < 90, ship the best version, list remaining gaps, and what evidence would push past 90 (do not fabricate).

---

## Memory commands

- On every run: load memory first.
- On "update my resume memory" / "save my profile" / similar: merge user changes into `.resume-memory/profile.json` without wiping untouched fields; confirm what changed.
- Details: [memory.md](references/memory.md).

---

## LaTeX + PDF rules

- **2 pages** when compiled. Check page count after compile; adjust spacing/content — do not claim page count without checking.
- ATS-safe: standard headings (`Education`, `Experience`, `Projects`, `Skills`, …), selectable text, no text boxes / icons / tables used as layout hacks that break parsers.
- Header: name + contact + **GitHub, LinkedIn, and every other link the user provided** (prominent).
- Define the person's work clearly: role, scope, stack, outcomes with metrics when available.
- Source vs output:
  - Write: `resume-build/src/<name>.tex`
  - Compile PDF: `resume-build/output/<name>.pdf`
  - Saved PDF: `resume-archive/pdf/` (only after page count is verified)
  - Log: `resume-tracker/resumes.xlsx` (`Name`, `Description`, `Yes/No`, `Date`, `Time`, `Role`, `Company`, `PDF`, `ATS`)

### Compile (prefer latexmk)

```bash
mkdir -p resume-build/src resume-build/output
latexmk -pdf -interaction=nonstopmode -output-directory=resume-build/output resume-build/src/<name>.tex
```

Fallback:

```bash
pdflatex -interaction=nonstopmode -output-directory=resume-build/output resume-build/src/<name>.tex
pdflatex -interaction=nonstopmode -output-directory=resume-build/output resume-build/src/<name>.tex
```

If TeX is missing: write the `.tex` files, report the exact install need (`TeX Live` / `MiKTeX`), and stop without inventing a PDF. Do not archive or log a row until a real PDF exists.

### Archive + Excel tracker (after a verified PDF)

Run from the hiring-agent repo root. Copies the build PDF into `resume-archive/pdf/` and appends one row. Description is one line (what this version emphasizes), not the full JD. Date and Time are filled automatically (local `YYYY-MM-DD` / `HH:MM`). Yes/No is an Excel dropdown the user can click.

`--decision yes` when the user accepts this version. `--decision no` when they reject it. Flip later: `python resume-tracker/track_resume.py set --row <n> --decision no`.

```bash
python resume-tracker/track_resume.py save --name "<person>" --description "<one line>" --decision yes --source resume-build/output/<name>.pdf --role "<target role>" --company "<target company>" --ats <score>
```

Needs `openpyxl` (`pip install -r requirements.txt`). Do not paste tracker rows or PDF bytes into the conversation; print the script's `archived=` and `row=` lines only.

On Windows PowerShell, create dirs with `New-Item -ItemType Directory -Force -Path resume-build/src, resume-build/output`.

---

## Token use (keep the workflow; cut chatter)

The 3-loop fit cycle, intake, humanize pass, and quality bar stay intact. Spend tokens on the resume, not on tooling:

- Read a reference file only for the step you are in. Do not paste those files into the reply.
- Log with `track_resume.py`. Do not hand-write `.xlsx` XML or restate the sheet.
- Description in the tracker is one line. Do not store the full job description there.
- Research budget: ≤3 searches + ≤2 page reads; keep a short note only — do not paste articles.
- Final report stays in the short format below.

## Quality bar (non-negotiable)

- Every section the user filled appears unless the template forbids it
- Keywords from JD appear naturally; no keyword stuffing
- Human voice: specific tools, scope, metrics; ban generic AI phrasing ([humanize.md](references/humanize.md))
- Links prominent in header
- Final report includes ATS score, loop changelog, and file paths

## Final report format

```
## Resume ready
- PDF: resume-build/output/<file>.pdf
- Saved: resume-archive/pdf/<stamped-file>.pdf
- TeX: resume-build/src/<file>.tex
- Tracker: resume-tracker/resumes.xlsx row <n> (Yes/No: Yes|No; Role; Company)
- Pages: 2 (verified)
- ATS/JD fit: <n>/100 (after 3 loops: L1=<a>, L2=<b>, L3=<c>)
- Hiring-agent: <score> (--role <name>) or "skipped: <reason>"
- Research: <query topics checked online, or "no web tool — used JD only">
- What changed: <short bullets from the 3 loops>
- Memory: .resume-memory/profile.json updated | unchanged
```
