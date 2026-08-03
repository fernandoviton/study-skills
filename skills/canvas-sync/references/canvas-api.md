# Canvas REST API notes for read-only sync

All requests: `GET <base_url>/api/v1/...` with header
`Authorization: Bearer $CANVAS_API_TOKEN`.

## Pagination

Every list endpoint paginates. Always pass `per_page=100`, and follow the
`Link` response header's `rel="next"` URL until it's absent. The `next` link
already carries all query params — request it verbatim. Do not assume one
page is everything; a course with 40 assignments will silently truncate at
the default page size of 10.

## Endpoints

| What | Endpoint | Useful params |
|---|---|---|
| Verify token | `/users/self` | — |
| Active courses | `/courses` | `enrollment_state=active`, `include[]=syllabus_body`, `include[]=term`, `include[]=teachers`, `include[]=total_scores` |
| Assignments | `/courses/:id/assignments` | `include[]=submission`, `order_by=due_at` |
| Announcements | `/announcements` | `context_codes[]=course_<id>`, `start_date=2000-01-01` (default window is only 14 days back) |
| Modules | `/courses/:id/modules` | `include[]=items` |
| Pages | `/courses/:id/pages` | then `/courses/:id/pages/:url` for the body |
| Files | `/courses/:id/files` | `sort=updated_at`, `order=desc` |
| Grades / status | comes free via `include[]=submission` on assignments and `include[]=total_scores` on courses | — |
| Calendar events | `/calendar_events` | `context_codes[]=course_<id>`, `start_date`, `end_date`, `type=event` |

## Field notes

- **Dates** are ISO 8601 UTC (`due_at`, `unlock_at`, `lock_at`, `posted_at`).
  Convert to the user's local timezone when writing markdown — a due date of
  `05:59Z` is usually 11:59 PM local the *previous* day.
- **`syllabus_body`**, assignment **`description`**, and announcement
  **`message`** are HTML. Convert to markdown.
- **Submission** (from `include[]=submission`): `workflow_state`
  (`unsubmitted` / `submitted` / `graded`), `score`, `submitted_at`,
  `late`, `missing`. This is what grades.md is built from.
- **File download**: the `url` field on a file object is a pre-signed
  download link that already honors the token; `size` is in bytes;
  `content-type` tells you whether it's a document or a video.
- **Assignment `points_possible`** can be null (ungraded items) — handle it.
- **`html_url`** on nearly every object is the human web link; include it in
  the markdown so the user can jump to Canvas.

## Errors and quirks

- `401 Unauthorized` — bad/expired token.
- `403 Forbidden` on a sub-resource — instructor disabled that tab for
  students. Skip it, note it, don't retry.
- Some endpoints return `while(1);` prefixed JSON in browser contexts — the
  API with a Bearer token does not, but strip it if it ever appears.
- Rate limiting exists (`403` with `Rate Limit Exceeded` body). Sequential
  requests won't hit it; if it appears, wait a few seconds and continue.
