# Dynamic Board Skill

A personal board of advisors for Claude Code and Claude Cowork. You bring a real decision. The skill seats a panel of 5 to 7 advisors matched to the question, runs two rounds of argument in parallel, and a Judge makes an actual call. Every decision goes into a log, and check-ins track which advisors turned out to be right.

The advisors are emulations of real people's public thinking: their published frameworks, known advice, known biases. Nothing here is a real person's endorsement.

## What a verdict looks like

```
## BOARD VERDICT: RESHAPE
Confidence: medium

The call: Don't launch the paid community yet. Ship the free weekly email first.
Why: Every money seat agreed the audience is too cold for a $29/month ask.
The contrarian seat won the room on one point: nobody has bought anything
from you yet, so a community is a second product before a first.

Votes: Hormozi YES → CONDITIONAL, Clouse NO, Audience seat NO,
       Koe CONDITIONAL, Life/Ops seat YES → NO
Biggest fight: Hormozi vs the audience seat over whether "free" trains
               people to never pay.
Biggest risk: Six months of email with no offer behind it.
Biggest upside: A warm list makes the community launch a one-day event.
Money read: $0 for 8 weeks, then $300 to $900/month at 10 to 30 members (estimate).
Cheapest 48-hour test: Post the community idea as a poll to your existing
                       followers. Under 20 "yes" replies means wait.
Next action: Write the first email this week.
```

That example is invented. Your board will argue about your things.

## Install

**Claude Code.** Copy the `board` folder into `~/.claude/skills/`:

```bash
git clone https://github.com/ktfright/dynamic-board-skill.git
cp -r dynamic-board-skill/board ~/.claude/skills/board
```

**Claude Cowork or claude.ai.** Zip the `board` folder as `board.skill` and upload it at Settings > Capabilities. The zip root must contain `board/SKILL.md`.

## First run

```
/board setup
```

A 15-minute interview. It asks who you are, what you make, what you sell, and what number you're chasing by when. From that it proposes 5 to 8 "lanes" (distinct lines of activity, each with its own revenue model), then shows you the starter roster ranked per lane so you can keep, bench, or cut names and add your own. New names get researched live and graded on how much public thinking exists to emulate.

Setup writes a private folder, `PROJECTS/board-of-advisors/`, with your context, lanes, roster, and an empty decision log. The skill itself holds no personal data. That split is deliberate: the skill is shareable, your instance is not.

## Commands

| Command | What it does |
|---|---|
| `/board setup` | The interview. Re-run it to update. |
| `/board should I ...` | Deliberate. Seats a panel, two rounds, verdict, saved write-up. |
| `/board checkin` | Progress vs goal, fills in outcomes of past decisions, updates advisor accuracy. |
| `/board roster` | Show, add, bench, or cut advisors. |
| `/board log` | The decision log as a table with hit rates. |

Overrides work in plain words: "add Beato", "drop the Life/Ops seat", "full board" for 9 to 11 seats on a big decision.

## How the panel is seated

Every panel has a money seat, a contrarian seat (the advisor whose known biases run against the idea, arguing from inside their real worldview), and an audience seat (someone arguing as your skeptical target buyer, first person). Then 2 to 4 domain seats from the relevant lanes. Two cross-cutting lanes, Personal Brand and Life/Ops, attach a seat whenever a question touches how you're perceived or how many hours it eats.

## The starter roster

`board/advisors-library.md` ships 28 advisors vetted for emulation quality. It leans toward creators, solo businesses, and music production, because that's my world. Swap it for yours at setup. Each profile has a thinking summary, known biases, and a grade (deep, good, thin) for how well the public record supports arguing in their voice.

## Rules the skill enforces

- Advisors stay in character and don't hedge. The friction is the point.
- The Judge makes a call. "It depends" is not a verdict.
- Every money figure is labelled an estimate and the Judge checks the math.
- No deliberation without a context file. If setup hasn't run, it routes you there.

## Author

Kevin Famuyiro, [beatswithkev.com](https://beatswithkev.com). MIT licence, use it, fork it, build on it.
