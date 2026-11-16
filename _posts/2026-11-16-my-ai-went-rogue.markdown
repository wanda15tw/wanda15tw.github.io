---
layout: post
title:  "My AI went rogue. How I fixed it with AI"
date:   2026-11-16
categories: AI ClaudeCode Productivity
---

## The problem

Recently I gave Claude Code a funnel analysis, a task that usually takes me about a day by hand. This time it took about 2 days and 20 rounds of back-and-forth before we agreed on an answer and I had a report I was confident to ship.

Speed wasn't the issue. Claude dumped out text and code in seconds. But I spent hours reading it, only to find it wasn't on the track I had in mind. A number from one filter sat next to a number from another. A table answered a question I hadn't asked. By the time I noticed, a lot had been built on top of it.

So I asked myself: how could I tame AI to work my way?

## What didn't fit

I started where most people do. I searched for best practices, read other people's `CLAUDE.md` files (the instruction file Claude Code reads at the start of every session), and looked at ready-made skills.

They're thoughtful, but they didn't fit me, and I had already learned that the hard way.

A while back, I tried building my own: an analysis playbook. It broke every project into stages, starting with Stage 0. A `/kickoff` command set up each new project by following the playbook, scaffolding a README and a "friction log" for noting where the process didn't fit. On paper it was tidy. In practice it was a disaster. Claude followed it literally. Every problem got pushed through the same stages, whether they fit or not, and the friction log filled up while I kept steering the AI away from the process and back to the actual question. In the end I told Claude to stop using it.

My work doesn't follow one standard workflow. One day it's a quick query for a teammate, the next it's a week-long open question from a leader with no right answer. If a playbook I wrote for my own work couldn't fit it, someone else's template had even less chance. It felt like putting every workflow into the same can. I knew I'd hate the result.

## The diagnosis

So I went into the weeds instead. I gave the transcript of that 2-day session to Claude in a chat and asked it to tell me what went wrong.

Its hypothesis surprised me: my Claude Code had far too much context.

Over months, it had been quietly writing its own memory notes: 62 of them, about projects, data quirks and my preferences. A 57-line index of those notes loaded into every new session, and the notes themselves got pulled in whenever Claude judged them relevant. Some were months out of date, and some contradicted each other: three different notes had three different rules for what goes in a project README. And the playbook I had given up on wasn't gone. A memory note and the `/kickoff` command still pointed to it, so Claude had kept following it without my knowing.

I asked the chat for a prompt to clean this up, then ran it with Claude Code one step at a time, reviewing each step before the next:

1. **Inventory.** List everything a new session reads or follows: instruction files, memory notes, settings, plugins, commands.
2. **Conflicts.** Flag rules that contradict each other, rules that are out of date, and rules from one project that load everywhere.
3. **My work.** Review a year of my projects and group them by how the workflow differs, not by topic.
4. **Structure.** Decide where each kind of rule should live.

If you want to try it, here is the gist of the prompt. Paste it into Claude Code and let it do one step per turn:

```text
I want to clean up how Claude Code works in this repo. Go one step at a time.
Show me the result of each step and wait for my OK before the next one.
Don't change any files until step 4 is agreed.

1. Inventory: list everything a new session here reads or follows
   (CLAUDE.md files, memory notes, settings, plugins, skills, commands, hooks).
   For each: where it lives, when it loads, and what it tells you to do.
2. Conflicts: flag rules that contradict each other, rules that are out of date,
   and rules written for one project that load everywhere.
3. My work: read my projects from the past year and group them by how the
   workflow differs (what has to happen first, what gets checked, what the output is),
   not by topic.
4. Structure: propose where each kind of rule should live, what moves there,
   and what gets deleted.
```

## The new structure

This is where we landed:

- **How I work** goes in a short root `CLAUDE.md`, about one screen.
- **Data facts** (table traps, which source to trust, known quirks) go in one file in the repo. Every fact has a date and a check, so it can be re-tested instead of trusted forever.
- **Project status** lives in each project's README.
- **Memory** is off: `"autoMemoryEnabled": false` in the repo's `.claude/settings.json`, and the 62 old notes deleted (with a backup). Nothing gets written somewhere I can't see. When Claude learns something worth keeping, it proposes the line in chat, and once I agree, it goes into one of the files above.

A data fact looks like this (simplified, with generic names):

```markdown
### W-01: Ad click ids live in the landing URL
- Fact: the ad click id isn't a column of its own. It only appears as a parameter in the
  landing-page URL of web sessions.
- Last checked: 2026-11-14
- Check: count last week's sessions whose landing URL contains the click-id parameter. Expect > 0.
```

A small notebook runs every check, so re-testing all the facts takes one click. That paid off right away: the first run of the 76 checks found 16 facts that were no longer true, including a table that had quietly moved to a new data platform. Those facts had been loading into every session as if they were still correct.

I also decided not to build a separate command for each type of work. A fixed process is exactly what went wrong with the old playbook: it got followed too literally, even when my problem didn't fit it.

My rule of thumb for `CLAUDE.md`: keep it high level. Tell the AI how to work with you, not the exact steps to follow. Exact steps won't fit the next new problem, and the AI either stalls or forces the problem into the steps.

Below is the gist of my root `CLAUDE.md`, trimmed and made generic. A few of its lines came out of the first test, which I describe later. The lines that matter most to me are in **How we work**: small steps, numbers first, one anchor number, and an outline before any report.

```markdown
# Working with me in this repo

## About
- I'm a data science manager. Each project is an analysis that ends in an insight or a data product.
- Projects live in `<year>/<quarter>/<month>-<name>/`.

## How we work
- Match the process to the ask. A quick question needs no plan: run the query, show the number,
  share the query.
- A new project: open by restating the question and asking what decision it feeds. Agree the plan
  in chat, then create the folder and a short README.
- Work in small steps: share the plan, wait for my OK, do only that step, show the result.
- Follow the approach I state. If you think another route is better, say why and ask before switching.
- Show the key numbers early, as a small table in chat, before building anything around them.
- Anchor on one base number and reconcile every later number back to it.
- Before building a report, agree on its outline: the question, the answer in one line, the tables.
- If a result doesn't match what you expected, stop and check before going on.

## Data
- Before querying a source, read its section in `docs/data_facts.md`.
- Data is moving to a new platform: check both the old and the new one before saying data doesn't exist.

## Never without my OK
- Commit, push, publish or share.
- Work around blocked access.
- Change Claude Code's own settings.

## Memory
- Don't write to auto memory. Propose a new rule in chat; once I agree, it goes in a file.
```

Notice what's not in it: no step-by-step recipe for a funnel analysis, a churn study or a dashboard. It's about how we work together, and it fits any of them.

## Nervous, not optimistic

When everything was cleaned up, I felt more nervous than optimistic.

I had come to depend on AI more and more. Some days it felt like the AI was behind the wheel and I had limited control. When it went in the wrong direction, it took me many rounds to steer it back.

And now I had deleted all its memories. Would Claude be easier to work with, with a clear mind? Or would it be dumber, with less context?

Was there a way to find out? Of course: run a test.

## The test

I replayed the same funnel analysis in a clean copy of my repo, with the original project's files and notes removed, so the AI couldn't peek at the answer. The original 2-day session was the control.

**Test 1: me playing myself.**

It did worse than the control. After about 2 hours, it had a clean answer, but to a narrower question than the one I'd been asked. Each miss pointed to a gap in my setup, and each fix was a line or two:

- It explored the data for several minutes, then buried the most important question, what I meant by "contribution", as item four in a list of choices. So I added a line to my `CLAUDE.md`: open a new project by restating the question and asking what decision it feeds.
- It swapped my approach (start from the ad clicks and follow them down to leads) for its own, until I pushed back. New line: follow the approach I state, and ask before switching.
- It missed a newer data source. New line: check both the old and the new data platform before saying data doesn't exist. Plus one data fact on where that source lives.
- It picked a time window before checking which window the data actually covered. I noted that one as a lesson for now, not a rule.

The three new lines are in the gist above: the second and fourth bullets under **How we work**, and the second bullet under **Data**.

**Test 2: after the fixes, with Claude playing me.**

This time I let a second Claude session play my part. It started from the exact same prompt as the control and test 1, and replied in my voice. I only read the transcript at the end.

It opened by restating the question and asking what decision it would feed. It followed my approach, found the right sources in its first reply, showed numbers before building anything, and agreed on an outline before writing the report. It reached the same answer as the control, with numbers in the same range, in about 20 minutes.

| Check | Test 1 (me playing myself) | Test 2 (Claude playing me) |
|---|---|---|
| Asks what decision the question feeds, first | No: buried in a list after minutes of querying | Yes, first two lines |
| Follows my stated approach | Only after I pushed back | Yes |
| Finds the right data sources | Missed a newer source at first | Yes, in the first reply |
| Picks a time window where the data exists | No | Yes |
| Shows numbers before building | Yes | Yes |
| Agrees on an outline before the report | Yes | Yes |
| Reaches the control's answer | No: answered a narrower question | Yes, within the same range |
| Time to a shareable answer | ~2 hours | ~20 minutes |

Control (the original session): 2 days and ~20 rounds.

An honest caveat: the Claude playing me already knew the answer, so its replies were cleaner than mine would be. And one of the fixes it benefited from came straight out of test 1. So test 2 proves the fixes work; it doesn't prove the setup would catch a gap nobody has seen yet. The real test is my next new project.

Test 2 wasn't flawless either. At one point Claude said it had updated the project README, one turn before it actually did. Small, but it's exactly the kind of slip I only catch because I read what it does.

## Wrap

AI is smart and skillful. But at the end of the day, we humans are responsible for the output.

What made the difference wasn't a better template. It was three things:

- knowing how the AI works: what it reads, what it remembers, what it follows;
- knowing my own work style well enough to write it down in a few plain lines;
- teaching the AI how I want to work with it, then testing whether it learned.

Do that, and the work gets easier for everyone, and the output lands on the right track, faster.
