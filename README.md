# study-tutor

A Claude Agent Skill that turns Claude into a tutor for coursework in any
subject — instead of a machine that does the coursework.

Works in Claude Code, Claude Cowork, and anything else that reads the
[Agent Skills](https://agentskills.io) format.

## What it does

- **Explains** concepts, after asking what you already think they mean
- **Quizzes** you from your own course materials
- **Reviews** work you've already written and names the problem without handing
  you the fix
- **Locates** where your readings address a topic, with page numbers
- **Plans** big assignments into steps you execute

## What it won't do

Two things, deliberately:

1. Write the thing you submit — essays, problem sets, lab reports, code
2. Supply the load-bearing idea — the thesis, the proof strategy, the
   interpretation

Everything else it answers straight. Definitions, formulas, dates, vocabulary,
citation format — no Socratic runaround. The rule is not "never give answers,"
it's "never do the part you're supposed to be learning."

If you're genuinely stuck, it escalates help in stages and gives you the answer
after three real attempts. It's a tutor, not a gatekeeper.

## Install

**Claude Code**

```bash
git clone https://github.com/<your-username>/study-tutor.git
mkdir -p ~/.claude/skills
cp -r study-tutor/skills/study-tutor ~/.claude/skills/
```

Restart Claude Code, or run `/reload-plugins`. That's it — the skill triggers on
its own when you're working on coursework. You don't have to invoke it.

**Claude Cowork / Claude Desktop**

Copy the `skills/study-tutor` folder into your skills directory, or upload
`SKILL.md` through Settings → Capabilities → Skills.

## Recommended: one CLAUDE.md per course

The skill handles *how* to tutor. It doesn't know your syllabus. Drop a
`CLAUDE.md` in each course folder with the specifics and the two work together
much better than either alone.

Copy `templates/course-CLAUDE.md` into a course folder, fill it in, and update
the "covered so far" section weekly. Takes two minutes.

## The workflow that actually works

Finish something badly, *then* hand it over.

Review mode is where most of the value is, and it's the mode students use least,
because it's more natural to ask for help before writing than after. A rough
complete draft plus a review pass beats a polished paragraph plus a
conversation, every time.

If you're coding: commit your own solution first, then ask for review. Your git
history also happens to be proof the work is yours.

## A note on academic integrity

Check your institution's AI policy before you start, not after something goes
wrong. Most schools have folded AI into their academic honesty definitions, and
the specifics vary by course and instructor.

If your course has a stated policy, put it in that course's `CLAUDE.md` — the
skill is written to defer to it over its own defaults.

## License

MIT. Fork it, change the rules, make it yours.
