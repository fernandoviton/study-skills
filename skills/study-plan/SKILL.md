---
name: study-plan
description: Turn synced Canvas data into a study plan through back-and-forth with the student — what to work on, when, and in what order — while learning their scheduling preferences over time. Use when the user asks what to work on, wants to plan a week or a big assignment, feels behind or overwhelmed by deadlines, or wants to review or adjust an existing plan.
---

# Study plan

Turn what's due into what to do. The plan is built *with* the student, not
handed to them — a plan they didn't shape is a plan they won't follow.

## Ground truth first

The facts come from the Canvas mirror, not from memory:

- `courses/*/canvas/assignments/` — what's due, when, for how many points,
  and whether it's already submitted
- `courses/*/canvas/grades.md` — where they actually stand in each course
- `courses/*/canvas/announcements.md` — instructor changes and hints
- `courses/*/CLAUDE.md` — the student's own notes on what each course demands

Check `.canvas/sync-state.json` before planning. If the last sync is more
than a day old — or the mirror doesn't exist — say so and offer to run
canvas-sync first. Planning against stale due dates is worse than not
planning.

## The conversation

Don't dump a schedule. Read the data, then ask the few questions the data
can't answer — and only those:

- How far along are they on anything already in flight?
- What time do they actually have this week (work shifts, practices, a life)?
- Which of the upcoming items scares them most? Fear is data: it usually
  marks the task that needs the earliest start.

Skip anything already answered in `planner/preferences.md`. Two or three
questions, then a draft plan, then revise it together. On a return visit,
start differently: open `planner/plan.md`, ask how it went, and carry the
honest answer — including "I did none of it" — into the next draft without
commentary.

## The plan file

Write the agreed plan to `planner/plan.md`, overwriting the old one (git
keeps history if they want it). Shape:

- A dated header and a one-line focus for the week.
- Per day: 1–3 concrete tasks, each tied to a real assignment and sized in
  hours. Name the thinking steps explicitly — "draft a thesis you could
  defend," not "work on essay."
- Buffer before every due date. A task finishing the hour it's due is a plan
  for failure.
- A "not this week" line for things deliberately deferred, so deferral is a
  decision instead of an accident.

Plan at most a week in detail; beyond that, just mark the dates that cast
shadows backward (the exam that needs two weeks of runway). Leave slack —
a plan that schedules every hour breaks the first time anything slips, and
everything slips.

## Prioritization is judgment, not sorting

Due date alone is a bad sort key. Weigh points, current course standing, and
how much of the grade is still in play. Fifty points in a course they're
acing matters less than twenty in one they're not. When two deadlines
genuinely collide, say which one should win and why — an honest "the lab
report matters more than the discussion post" beats a diplomatic schedule
that pretends both fit.

If the week genuinely doesn't fit, say that too, plainly, and help them
choose what slips. Don't compress estimates until the overload disappears on
paper.

## Learning their preferences

`planner/preferences.md` is the memory between sessions. When the student
states or reveals something durable — "I can't do problem sets at night,"
"give me deadlines two days early," "stop scheduling Saturdays," a plan
format they keep reformatting — write it there and say you did, in one line.
The test for durable: would this be true next month? "I'm busy Thursday" is
not a preference; "Thursdays are always shot" is.

Read the file at the start of every session and apply it silently. It
belongs to the student — if they edit or delete entries, that's the new
truth. Keep entries short, dated, and in their words where possible.

## Boundaries

Plan the work; don't start doing it. When the session drifts from "when do
I write the essay" to "help me write the essay," that's study-tutor's job —
follow its rules, not this file's. And this skill never writes to Canvas;
nothing here does.
