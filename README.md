# study-skills

Three Claude Agent Skills that work together to run your semester: pull your
courses from Canvas, plan your week, and get tutored through the actual work —
without Claude doing the work for you.

Works in Claude Code, Claude Cowork, and anything else that reads the
[Agent Skills](https://agentskills.io) format.

## The three skills

**canvas-sync** — mirrors your Canvas courses into local markdown: syllabi,
assignments with due dates and rubrics, announcements, grades, course files.
Strictly read-only — it never sends a write to Canvas, so it can't break
anything. After each sync it reports what changed: new assignments, moved
due dates, new grades.

**study-plan** — reads the mirror and builds a weekly plan *with* you, not
for you. It asks the few things Canvas can't know (your hours, your
progress, what you're dreading), weighs points and course standing instead
of just sorting by due date, and tells you honestly when a week doesn't fit.
It keeps a `planner/preferences.md` of how you like to work — say "never
schedule me Saturdays" once and it sticks.

**study-tutor** — for sitting down and doing the work. Point it at an
assignment; it pulls the real spec from the mirror, helps you start, explains
concepts, quizzes you, and reviews your drafts. It will not write what you
submit or hand you the idea the assignment is testing — but everything else
(definitions, formulas, how a library works) it answers straight. If your
course has a stated AI policy, the tutor follows it over its own defaults.

The loop: **sync** at the start of the week → **plan** what to do → **tutor**
while you do it. Each skill triggers on its own when you talk about the
relevant thing; you never have to invoke them by name.

## Install

**Claude Code**

```bash
git clone https://github.com/fernandoviton/study-skills.git
mkdir -p ~/.claude/skills
cp -r study-skills/skills/canvas-sync study-skills/skills/study-plan study-skills/skills/study-tutor ~/.claude/skills/
```

Restart Claude Code, or run `/reload-plugins`.

**Claude Cowork / Claude Desktop**

Copy the three folders under `skills/` into your skills directory. Keep the
folder names — each must match the `name` in its skill's frontmatter or it
won't load.

## Canvas setup (one time)

You need an API token from your school's Canvas:

1. In Canvas: **Account → Settings → + New Access Token**. Name it something
   like "study-skills (read only)" and give it an expiry date.
2. Put the token in the `CANVAS_API_TOKEN` environment variable in your
   shell profile. Don't put it in a file — and especially not in a folder
   you might commit.
3. In your study folder, say "set up canvas" — the skill asks for your
   school's Canvas URL, saves it to `.canvas/config.json`, and verifies the
   token works.

Then "sync canvas" any time. First sync inventories everything; later syncs
tell you what changed.

## Folder layout

Run Claude from one study folder (make it a git repo — free history for your
plans and drafts). The skills maintain this shape:

```
.canvas/                 config + sync state
courses/<course>/
  canvas/                synced mirror — sync overwrites this, you don't edit it
  CLAUDE.md              your course context (AI policy, constraints) — see templates/
  notes/  drafts/        yours; no skill will ever touch them
planner/
  plan.md                the current plan
  preferences.md         what the planner has learned about how you work
study-log.md             optional session log the tutor appends to
```

## Recommended: one CLAUDE.md per course

Canvas knows your due dates; it doesn't know your instructor's AI policy or
what the course is actually trying to teach you. Copy
`templates/course-CLAUDE.md` into each course folder and fill it in — takes
two minutes, and both the planner and tutor get noticeably better.

## The workflow that actually works

Finish something badly, *then* hand it over. The tutor's review mode is
where most of the value is, and it's the mode students use least, because
it's more natural to ask for help before writing than after. A rough
complete draft plus a review pass beats a polished paragraph plus a
conversation, every time.

If you're coding: commit your own solution first, then ask for review. Your
git history also happens to be proof the work is yours.

## A note on academic integrity

Check your institution's AI policy before you start, not after something
goes wrong. Most schools have folded AI into their academic honesty
definitions, and the specifics vary by course and instructor. Put each
course's stated policy in its `CLAUDE.md` — the tutor is written to defer
to it, in both directions.

## License

MIT. Fork it, change the rules, make it yours.
