# Built-in resume formats

Single-column LaTeX. ATS-safe: standard headings, real text, no icons, no text boxes, no sidebar columns.

Ask the user to pick **one name** in Step 2. If they say default, or do not care, use `classic`. If they paste their own template, follow that instead and ignore this list.

| Name | File | Use when |
|------|------|----------|
| classic | `assets/formats/classic.tex` | General applications. Summary, experience, projects, education, skills, awards. |
| compact | `assets/formats/compact.tex` | A lot of roles must fit on 2 pages. Tighter type and spacing. |
| skills-first | `assets/formats/skills-first.tex` | The job description is tool-heavy. Skills sit directly under the header. |
| academic | `assets/formats/academic.tex` | Research, teaching, publications. Education and publications lead. |
| projects-first | `assets/formats/projects-first.tex` | Students, career switchers, and builders. Projects lead, then experience. |

Same header macros in every file: `\Name`, `\Email`, `\Phone`, `\Location`, `\LinkedIn`, `\GitHub`, `\Portfolio`. Add other user links in the header line. Do not drop a filled section just because the sample order differs; move it, do not delete it.

Copy the chosen file to `resume-build/src/<slug>.tex` and fill it from memory. Do not mix two formats in one file.
