# Resume Forage

Resume Forage builds a 2-page, job-specific resume in LaTeX, scores it until it is the best honest fit for the job description, compiles a PDF, saves that PDF in its own folder, and logs the result in an Excel sheet.

Open this repository as the project folder in an agent (Cursor, OpenCode, Antigravity, Claude Code, Codex, or Hermes) and start a chat.

If your profile is not finished, the agent starts on its own:

**Let's create your profile.**

It asks **one question**, waits for your answer, saves it, then asks the next. It does not send the whole list at once. When the profile is complete, it asks which built-in format you want and then takes the job description.

You can also say **build my best-fit resume** or **update my resume memory**.

The agent follows `.agents/skills/best-fit-resume/SKILL.md`. Adapters in `.cursor/skills/`, `.claude/skills/`, and `.opencode/skills/` point at that same file, so every harness runs one workflow. Cursor also loads `.cursor/rules/start-profile.mdc` on every chat.

## What you get

- One intake pass. Every profile field is asked up front and stored so later runs do not start from zero.
- Your template, or the built-in 2-page ATS layout.
- A short web check of the role, the company, and current ATS practice before writing.
- Three score-and-rewrite loops aimed at 90 or higher.
- Human wording with generic AI phrases removed.
- Header links for GitHub, LinkedIn, and any other URLs you gave.
- A `.tex` file, a compiled PDF, a saved copy of that PDF, and one Excel row.

The agent only uses facts you provided. It does not invent employers, dates, tools, or metrics to chase a score.

## One-time setup

Requirements:

- Python 3.10 or newer
- `pip`
- A TeX install with `latexmk` or `pdflatex` (TeX Live or MiKTeX) if you want a PDF
- A model key only if you want `score.py` to call an LLM (see below)

```bash
git clone https://github.com/sujay119/Resumeforage.git
cd Resumeforage
pip install -r requirements.txt
```

Copy the model example if you want hiring-agent scoring:

```bash
copy .env.example .env
```

On macOS or Linux use `cp .env.example .env`. Edit `.env`:

- `DEFAULT_MODEL` must be a model listed in `providers.json` (Ollama examples such as `gemma4:latest`, or Gemini such as `gemini-2.5-flash`).
- `GEMINI_API_KEY` is required only when the chosen provider needs it.

`.env` is gitignored. Do not commit it.

If TeX is missing, the agent still writes the `.tex` file and tells you to install TeX Live or MiKTeX. It does not invent a PDF, and it does not log a tracker row until a real PDF exists.

If no model is configured, the agent still runs the 0–100 ATS rubric by hand and says that `score.py` was skipped.

## How to run it

Open the cloned folder as the workspace. Then use any of these:

- build my best-fit resume
- tailor my resume to this job description
- update my resume memory

### Where each harness loads the skill

| Harness | What it reads |
|---|---|
| Cursor | `.cursor/skills/best-fit-resume/SKILL.md` |
| OpenCode | `.opencode/skills/best-fit-resume/` and `.agents/skills/` |
| Antigravity | `.agents/skills/best-fit-resume/SKILL.md` |
| Claude Code | `.claude/skills/best-fit-resume/SKILL.md` and `CLAUDE.md` |
| Codex, Hermes, others | `AGENTS.md`, which points at `.agents/skills/best-fit-resume/SKILL.md` |

## The run, in order

The agent finishes the profile before it asks for a template or a job description. It does not repeat a question you already answered.

### 1. Profile, one question at a time

Opening a chat starts this step when `.resume-memory/profile.json` is missing or incomplete. The agent asks the next empty item only, saves your answer, then continues. The questions, in order, are:

- Full name, email, phone, location, target role
- GitHub, LinkedIn, portfolio, and any other links (label plus URL)
- Every job: company, title, location, dates, employment type, and 3–6 bullets with what you owned, the stack, and real metrics
- Internships, contract, freelance, research, and teaching, in the same shape
- Constraints (NDAs, employers you cannot name)
- Projects: name, purpose, your role, stack, repo or demo link, outcomes, personal or open source
- Education: school, degree, field, dates, optional GPA, coursework, honors
- Skills you want listed, and skills that must not be claimed
- Awards, publications, competitions, certifications, volunteer or leadership, spoken languages
- Strongest quantified wins
- Page limit (default 2), sections to keep or drop, tone, optional work-authorization note, filename slug

Answers are merged into `.resume-memory/profile.json`. Filled fields are never deleted unless you explicitly clear them.

Update later by saying **update my resume memory**, **save my profile**, or **remember this for my resume**. Lists match existing jobs and projects and update in place.

`profile.json` is gitignored because it holds contact details. `profile.schema.json` and `profile.example.json` are the empty shape you can look at.

### 2. Template

The agent asks you to pick a built-in format, or to paste your own. All built-ins are single-column LaTeX so applicant-tracking systems can read them. Say the name, or say **default** for classic.

| Say this | What you get |
|---|---|
| classic | Summary, experience, projects, education, skills, awards. The general layout. |
| compact | Smaller type and tighter spacing when you have many roles. |
| skills-first | Skills directly under your name, for tool-heavy job descriptions. |
| academic | Education, research, and publications first. |
| projects-first | Projects and repos first, then jobs. Fits students and builders. |

Files live in `.agents/skills/best-fit-resume/assets/formats/`. A pasted template is followed instead: section order, density, fonts, and margins. Every section you filled still appears.

### 3. Job description, research, three loops

Paste the full job description or give a file path. If the target role or company is not obvious, the agent asks for those two fields here. It does not reopen the whole profile.

Then, before any rewrite loop, it researches the public web:

- At most 3 searches and 2 page reads
- Role plus company: title variants and skills similar listings emphasize
- Current ATS practice: keyword placement, standard headings, measurable bullets, parseable layout

If that harness has no web tool, it says so and continues from the job description and any pages you pasted. Research is a short note. Articles are not pasted into the chat or the resume. Keywords are added only when your profile supports them.

Each of the three loops does this, in order:

1. Add honest keywords from the job description and the research note. No stuffing.
2. Score ATS fit from 0 to 100.
3. Score with this repo when a PDF and a model are available: `python score.py <pdf> --role <role>`.
4. Humanize the draft.
5. Rewrite the gaps and score again.

ATS points:

| Part | Max |
|---|---|
| Keyword coverage | 30 |
| Role clarity | 15 |
| Evidence and metrics | 15 |
| ATS parseability | 15 |
| Section completeness, including header links | 10 |
| Order and 2-page fit | 10 |
| Human voice | 5 |

Target is **90 or higher**. After loop 3, if the score is still under 90, the agent ships the best version and lists the gaps. It does not fabricate evidence to cross 90.

The included role rubric is `roles/software_engineering_intern/`. For another job:

```bash
python score.py --init-role backend_engineer
```

Edit `roles/backend_engineer/role.json`, `criteria.jinja`, and `system_message.jinja`, then score with `--role backend_engineer`.

Humanize rules live in `.agents/skills/best-fit-resume/references/humanize.md`. The draft drops filler such as “results-driven,” “leveraged,” “passionate about,” and “proven track record,” and it does not hide keywords in white text or invisible characters.

### 4. Additions

The agent asks if you want anything added or changed, applies that, and humanizes again.

### 5. Files

| What | Where |
|---|---|
| LaTeX source | `resume-build/src/<name>.tex` |
| Compiled PDF | `resume-build/output/<name>.pdf` |
| Saved PDF copy | `resume-archive/pdf/<date-time>-<name>.pdf` |
| Excel log | `resume-tracker/resumes.xlsx` |
| Profile | `.resume-memory/profile.json` |

The header shows your name, contact details, GitHub, LinkedIn, and every other link you provided. Headings stay standard (`Experience`, `Projects`, `Education`, `Skills`) so parsers can read the file. The agent compiles and checks that the PDF is 2 pages, then tightens or expands until it is.

Compile (the agent runs this):

```bash
latexmk -pdf -interaction=nonstopmode -output-directory=resume-build/output resume-build/src/<name>.tex
```

If `latexmk` is missing, it runs `pdflatex` twice with the same output directory.

On Windows, folders are created with:

```powershell
New-Item -ItemType Directory -Force -Path resume-build/src, resume-build/output, resume-archive/pdf
```

### Report you should see

```text
## Resume ready
- PDF: resume-build/output/<file>.pdf
- Saved: resume-archive/pdf/<stamped-file>.pdf
- TeX: resume-build/src/<file>.tex
- Tracker: resume-tracker/resumes.xlsx row <n>
- Pages: 2 (verified)
- ATS/JD fit: <n>/100 (after 3 loops: L1=<a>, L2=<b>, L3=<c>)
- Hiring-agent: <score> or skipped: <reason>
- Research: <query topics, or no web tool — used JD only>
- What changed: <short bullets>
- Memory: .resume-memory/profile.json updated | unchanged
```

## Excel tracker

The sheet is created the first time a real PDF is saved. Columns:

**Name, Description, Yes/No, Date, Time, Role, Company, PDF, ATS**

- **Description** is one line about what that version emphasizes, not the full job description.
- **Yes/No** is a dropdown on the cell (`Yes` or `No`). `Yes` means you accepted that version. `No` means you rejected it. You can change it in Excel or with the command below.
- **Date** is `YYYY-MM-DD` and **Time** is `HH:MM`, local time, filled when the row is saved.
- **PDF** is the path under `resume-archive/pdf/`.
- **ATS** is the score after the three loops.

The agent logs with:

```bash
python resume-tracker/track_resume.py save --name "<person>" --description "<one line>" --decision yes --source resume-build/output/<name>.pdf --role "<target role>" --company "<target company>" --ats <score>
```

Change a decision:

```bash
python resume-tracker/track_resume.py set --row <n> --decision no
```

Print the sheet:

```bash
python resume-tracker/track_resume.py list
```

`resumes.xlsx` and the PDFs under `resume-archive/pdf/` stay on your machine. They are gitignored.

## Layout of this repo

```text
.agents/skills/best-fit-resume/     full skill, questions, rubric, humanize rules, default .tex
.cursor/ .claude/ .opencode/        adapters that load the same skill
.resume-memory/                     profile schema and your local profile.json
resume-build/src/                   LaTeX
resume-build/output/                compiled PDF
resume-archive/pdf/                 saved PDF copies
resume-tracker/track_resume.py      writes the Excel sheet
resume-tracker/resumes.xlsx         created on first save
score.py                            hiring-agent PDF scorer
roles/                              rubrics used by score.py
prompts/templates/                  section extraction prompts
.env.example                        model settings
```

## What is not committed

- `.env`
- `.resume-memory/profile.json`
- PDFs and `.tex` drafts
- `resume-tracker/resumes.xlsx`
- LaTeX build junk (`.aux`, `.log`, and similar)

Clone the repo on another machine and your personal profile, resumes, and sheet stay behind. Say **update my resume memory** on the new machine and fill the profile again, or copy `profile.json` yourself.
