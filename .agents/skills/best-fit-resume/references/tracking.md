# Resume archive + Excel tracker

Separate from build output. Do not mix with `resume-build/`.

| Path | Role |
|------|------|
| `resume-build/output/` | Compile target only |
| `resume-archive/pdf/` | Saved finished PDFs |
| `resume-tracker/resumes.xlsx` | Name, Description, Yes/No, Date, Time, Role, Company, PDF, ATS |
| `resume-tracker/track_resume.py` | Only way agents should write the sheet |

Yes/No is an Excel **data-validation dropdown** on column C (`Yes` / `No`). Date = `YYYY-MM-DD`, Time = `HH:MM` (local), filled automatically on save.

## When to log

After a real PDF exists and page count is verified. Never invent a PDF row.

## Commands (repo root = this hiring-agent folder)

```bash
python resume-tracker/track_resume.py save \
  --name "<person>" \
  --description "<one line: emphasis>" \
  --decision yes \
  --source resume-build/output/<name>.pdf \
  --role "<target role>" \
  --company "<target company>" \
  --ats <score>
```

Flip decision later:

```bash
python resume-tracker/track_resume.py set --row <n> --decision no
```

List:

```bash
python resume-tracker/track_resume.py list
```

`--decision`: `yes` or `no` only. Description: one line, not the full JD. Requires `openpyxl` (`pip install -r requirements.txt`).
