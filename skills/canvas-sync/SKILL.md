---
name: canvas-sync
description: Pull courses, assignments, syllabi, announcements, modules, grades, and files from Canvas LMS into a local markdown mirror. Strictly read-only — never writes anything to Canvas. Use when the user asks to sync, refresh, or set up Canvas, pull their courses or assignments, or when another study skill finds the local Canvas mirror missing or stale.
---

# Canvas sync

Mirror the student's Canvas courses into local markdown files so the other
study skills (study-plan, study-tutor) can work offline against real course
data instead of the student's memory of it.

## Read-only, absolutely

Every request to Canvas is a GET. Never send POST, PUT, PATCH, or DELETE to
the Canvas API for any reason — not to mark an announcement read, not to
update a to-do item, not even if the user asks. If the user wants something
changed on Canvas, tell them to do it in the Canvas UI. This is the one rule
with no exceptions: the token may have full account permissions, and this
skill's promise is that it can't break anything.

## Setup (first run)

Two things are needed: a base URL and a token.

1. **Base URL** lives in `.canvas/config.json`:

   ```json
   { "base_url": "https://<school>.instructure.com" }
   ```

   If the file doesn't exist, ask the user for their school's Canvas web
   address and create it.

2. **Token** lives in the `CANVAS_API_TOKEN` environment variable — never in
   a file, and never in this repo. If the variable is unset, walk the user
   through it:

   - In Canvas: Account → Settings → scroll to Approved Integrations →
     **+ New Access Token**. Name it something like "study-skills (read only)"
     and set an expiry date.
   - Set the variable in their shell profile (or for the session), then
     restart Claude Code so it's inherited.

   Never echo the token, log it, paste it into a file, or include it in
   command output the user might commit. When testing the connection, pipe
   responses so the `Authorization` header is never printed.

Verify setup with `GET /api/v1/users/self` — a 200 with the user's name means
it works. A 401 means the token is wrong or expired.

## The local mirror

Everything lands under the folder the user runs Claude in:

```
.canvas/
  config.json          base URL (user-created, don't overwrite)
  sync-state.json      last sync time + compact index for diffing
courses/<slug>/
  canvas/              THE MIRROR — this skill owns it, may overwrite freely
    course.md          name, code, term, instructor, links
    syllabus.md        syllabus body, converted to markdown
    assignments/       one file per assignment: <slug>.md
    announcements.md   newest first
    modules.md         module structure with items
    grades.md          current scores and submission status
    files.md           listing of all course files with sizes + Canvas URLs
    files/             downloaded copies of the small ones
  ...                  everything else in courses/<slug>/ is the user's —
                       CLAUDE.md, notes, drafts. NEVER touch it.
```

`<slug>` is the course code, lowercased and hyphenated (e.g. `hist-1301`).
If two active courses share a code, append the term.

The boundary matters: inside `courses/<slug>/canvas/` you may delete and
rewrite at will to match Canvas. Outside it, write nothing.

## How to sync

Endpoint details, pagination, and field notes are in
[references/canvas-api.md](references/canvas-api.md) — read it before making
requests. The mechanics:

1. Read `.canvas/sync-state.json` if it exists (previous index, for the diff).
2. Fetch active courses, then per course: syllabus, assignments (with
   submission status), announcements, modules, files listing, grades.
   Write/overwrite the mirror files.
3. Download files that look like course documents (pdf, docx, txt, md, code)
   and are under ~2 MB into `files/`. List everything else in `files.md` with
   its Canvas URL — don't pull lecture videos onto the student's disk.
4. Write the new `sync-state.json`: ISO timestamp plus a compact index —
   per course, each assignment's id, name, due date, and points, and the ids
   of seen announcements. This is what makes the next sync's diff cheap.
5. Convert HTML bodies (syllabus, assignment descriptions, announcements) to
   readable markdown. Strip tracking cruft; keep links, lists, and tables.

It's fine to write a throwaway script in the scratchpad to do the fetching —
often cleaner than dozens of individual curl calls. Requests can be
sequential; there's no hurry that justifies hammering the API.

## The sync report

The report is half the value. After writing the mirror, compare against the
previous index and tell the user what actually changed:

- **New assignments**, with due dates and points
- **Changed due dates** — old date → new date, called out loudly
- **New announcements**, one line each
- **Grade changes** since last sync

If nothing changed, say so in one line. If this is the first sync, skip the
diff and give a one-paragraph inventory per course instead. Suggest running
study-plan if the changes affect what's due soon.

## Errors

- **401** — token expired or revoked; point at the setup steps.
- **403 / missing sections** — some instructors hide grades, files, or the
  syllabus. Note what was inaccessible in the report and move on; don't retry.
- **Course ended mid-term** — if a previously synced course stops appearing
  in active enrollments, leave its mirror in place and mention it, rather
  than deleting the student's record of it.
