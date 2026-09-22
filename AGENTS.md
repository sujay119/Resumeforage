# Agent instructions (hiring-agent)

## Best-fit resume skill

When the user wants a tailored/optimized resume, LaTeX→PDF resume, JD keyword fit, ATS score loop, or to update resume memory, load and follow:

**`.agents/skills/best-fit-resume/SKILL.md`**

(Same skill is discoverable under `.cursor/skills/`, `.claude/skills/`, and `.opencode/skills/` as thin adapters pointing at the canonical path.)

### Quick invoke phrases

- "Build my best-fit resume"
- "Tailor my resume to this JD"
- "Update my resume memory"

### Memory

`.resume-memory/profile.json` — durable profile; never drop filled fields on merge.

### Scoring

Use this repo's `score.py` and `roles/<role>/` rubrics inside the skill's 3-loop fit cycle. Do not replace with a weaker homemade scorer.

### PDF layout

- TeX: `resume-build/src/`
- Build PDF: `resume-build/output/`
- Saved PDFs: `resume-archive/pdf/`
- Tracker: `resume-tracker/resumes.xlsx` via `python resume-tracker/track_resume.py save --name ... --description ... --decision yes|no --source ... --role ... --company ... [--ats N]`
- Columns: Name, Description, Yes/No (dropdown), Date, Time, Role, Company, PDF, ATS
