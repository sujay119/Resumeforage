# Resume memory

## Location

- Profile: `.resume-memory/profile.json`
- Schema: `.resume-memory/profile.schema.json`

Persistent across sessions. Load **before** asking questions.

## Load

1. If `.resume-memory/profile.json` exists, read it.
2. Summarize non-empty fields to the user.
3. Ask only for missing fields or requested changes (still in Step 1 — one message).

## Merge rules

- Deep-merge updates into the existing JSON.
- **Never delete** a filled field unless the user explicitly clears it.
- Lists (work, projects, …): match by stable keys (`company+title+startDate`, project `name`); update in place; append new; only remove if user says to remove.
- Always set `updated_at` (ISO-8601). Keep `created_at` on first write.

## Explicit update commands

Treat these as memory-edit mode (may skip template/JD if user only wants memory updated):

- "update my resume memory"
- "save my profile"
- "remember this for my resume"
- "add … to my resume memory"

Confirm a short diff of changed keys after writing.

## Privacy

Do not commit secrets. `profile.json` should stay local (gitignored). Schema and README in `.resume-memory/` may be committed.
