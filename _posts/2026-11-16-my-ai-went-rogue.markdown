---
layout: post
title:  "My AI went rogue. How I fixed it with AI"
date:   2026-11-16
categories: AI ClaudeCode Productivity
---

# My AI went rogue. How I fixed it with AI

## The problem

I gave Claude Code a funnel analysis. Done by hand, it takes me about a day. With Claude, it took 3 days and
about 20 rounds of back-and-forth before we agreed on the answer and shipped a report I was happy with.

What bothered me wasn't the speed of the AI. Claude produced text and code in seconds. The problem was me: I spent
hours reading what it produced, only to find it wasn't on the track I had in mind. A number from one filter sat
next to a number from another. A table answered a question I hadn't asked. By the time I noticed, a lot had been
built on top of it.

So I asked myself: how do I get AI to work my way?

## What didn't fit

I started where most people do. I searched for best practices, read other people's `CLAUDE.md` files (the
instruction file Claude Code reads at the start of every session), and looked at ready-made skills.

They're thoughtful, but they didn't fit me. My work doesn't follow one standard workflow. One day it's a quick
query for a teammate, the next it's a week-long open question from a leader with no right answer. Fitting all of
that into someone else's template felt like putting every workflow into the same can. I knew I'd hate the result.

## The diagnosis

So I did something simpler. I gave the transcript of that 3-day session to Claude in a chat and asked it to tell
me what went wrong.

Its hypothesis surprised me: my Claude Code had far too much context.

Over months, it had been quietly writing its own memory notes: dozens of them, about projects, data quirks and my
preferences. Every one loaded into every new session, whether it was relevant or not. One note even pointed to an
old "playbook" folder I didn't trust, and Claude had been following it without my knowing.

I asked the chat for a prompt to clean this up, then ran it with Claude Code one step at a time, reviewing each
step before the next:

1. **Inventory.** List everything a new session reads or follows: instruction files, memory notes, settings,
   plugins, commands.
2. **Conflicts.** Flag rules that contradict each other, rules that are out of date, and rules from one project
   that load everywhere.
3. **My work.** Review a year of my projects and group them by how the workflow differs, not by topic.
4. **Structure.** Decide where each kind of rule should live.

## The new structure

Here is where we landed:

- **How I work** goes in a short root `CLAUDE.md`, about one screen.
- **Data facts** (table traps, which source to trust, known quirks) go in one file in the repo. Every fact has a
  date and a check, so it can be re-tested instead of trusted forever.
- **Project status** lives in each project's README.
- **Memory** is off. Nothing gets written somewhere I can't see.

I also decided not to build a separate command for each type of work. A fixed process is exactly what went wrong
with the old playbook: it got followed too literally, even when my problem didn't fit it.

My rule of thumb for `CLAUDE.md`: keep it high level. Tell the AI how to work with you, not the exact steps to
follow. Exact steps won't fit the next new problem, and the AI either stalls or forces the problem into the steps.

A few lines from mine:

- Work in small steps: share the plan, wait for my OK, do only that step, show the result.
- Show the key numbers early, as a small table, before building anything around them.
- Anchor on one base number and reconcile every later number back to it.
- Before building a report, agree on its outline: the question, the answer in one line, and the tables.

Making the data facts checkable paid off right away. The first run of the checks found 16 facts that were no longer
true, including a table that had quietly moved to a new data platform. Those facts had been loading into every
session as if they were still correct.

## Nervous, not optimistic

When everything was cleaned up, I felt more nervous than optimistic.

I had come to depend on AI more and more. Some days it felt like the AI was behind the wheel and I had limited
control. When it went in the wrong direction, it took me many rounds to steer it back.

And now I had deleted all its memories. Would Claude be easier to work with, with a clear mind? Or would it be
dumber, with less context?

Was there a way to find out? Of course: run a test.

## The test

I replayed the same funnel analysis in a clean copy of my repo, with the original project's files and notes
removed, so the AI couldn't peek at the answer. The original 3-day session was the control.

**Test 1: me playing myself.**

At first it did worse than the control. It explored the data for several minutes and then buried the most
important question, what I meant by "contribution", as item four in a list of choices. It swapped my approach
(start from the ad clicks and follow them down to leads) for its own, until I pushed back. It missed a newer data
source, and it picked a time window before checking which window the data actually covered.

But each miss pointed to something missing from my `CLAUDE.md` or my data facts, and each was easy to fix: four short
lines and one new data fact. For example: "Follow the approach Wanda states. If you think another route is better,
say why and ask before switching."

**Test 2: after the fixes.**

This time it opened by restating the question and asking what decision it would feed. It followed my approach,
found the right sources in its first reply, showed numbers before building anything, and agreed the outline with
me before writing the report. It reached the same answer as the control, within the same range, in about 20
minutes.

| Check | Test 1 (me playing myself) | Test 2 (Claude playing me) |
|---|---|---|
| Asks what decision the question feeds, first | No: buried in a list after minutes of querying | Yes, first two lines |
| Follows my stated approach | Only after I pushed back | Yes |
| Finds the right data sources | Missed a newer source at first | Yes, in the first reply |
| Picks a time window where the data exists | No | Yes |
| Shows numbers before building | Yes | Yes |
| Agrees an outline before the report | Yes | Yes |
| Time to a shareable answer | ~2 hours | ~20 minutes |

Control (the original session): 3 days and ~20 rounds.

An honest caveat: in test 2, I let Claude play me. It already knew the answer, so its prompts were cleaner than
mine would be. And one of the fixes it benefited from came straight out of test 1. So test 2 proves the fixes work;
it doesn't prove the setup would catch a gap nobody has seen yet. The real test is my next new project.

## Wrap

AI is smart and skillful. But at the end of the day, we humans are responsible for the output.

What made the difference wasn't a better template. It was three things:

- knowing how the AI works: what it reads, what it remembers, what it follows;
- knowing my own work style well enough to write it down in a few plain lines;
- teaching the AI how I want to work with it, then testing whether it learned.

Do that, and the work gets easier for everyone, and the output lands on the right track, faster.
