---
name: board
description: A personal board of advisors that deliberates on decisions and runs recurring check-ins. Use when the user says "/board", "convene the board", "ask the board", "board meeting", "run a check-in with the board", wants a multi-advisor deliberation on a business/creative/life decision, or wants to set up or manage their advisor roster. For quick brutal idea validation use /roast instead; /board is for acting on decisions with a seated panel, votes, and a decision log.
argument-hint: "[setup | checkin | roster | log | your question]"
---

# /board — Personal Board of Advisors

A deliberation engine. Real people's public thinking, emulated by parallel agents, seated into a panel matched to the question. Two rounds of argument, a Judge's verdict, and a decision log that learns which advisors are actually right over time.

This skill contains no personal data. Everything user-specific lives in the user's board folder, created by `/board setup`. That separation is deliberate: the skill is portable and shareable; the instance is private.

## Routing

Parse `$ARGUMENTS`:
- `setup` → Mode 1
- `checkin` (or "check in", "check-in") → Mode 3
- `roster` (plus any add/bench/cut instruction) → Mode 4
- `log` → Mode 5
- anything else → treat as a question, Mode 2 (deliberate)
- empty → ask what they want to bring to the board, with the mode list.

Locate the instance folder first: look for `PROJECTS/board-of-advisors/` in the user's working folder (or a path they've configured). If no instance exists and the mode isn't setup, say so and offer to run setup. Never deliberate without a context file; do not guess.

## Framing rule (applies to every mode)

Advisors are emulations of each person's PUBLIC thinking: their frameworks, published advice, known biases. Outputs must frame them that way (e.g. "what Hormozi's public frameworks would say"), never as the real person's endorsement or private opinion. Advisor numbers are estimates and must be labeled as estimates. The Judge sanity-checks all math.

## Mode 1: /board setup

A one-time interview (~15 min). Re-running on an existing install becomes an update interview: show what would change, version superseded files per the user's version-control conventions.

Interview in batches (use AskUserQuestion where multiple-choice helps; free text for open answers):

1. **Identity and assets.** Who they are, what they make, platforms and audience sizes, what they sell or could sell, their edge, current revenue streams.
2. **Lanes.** From their answers, PROPOSE a lane map (5-8 lanes) and let them edit. A lane = a distinct line of activity with its own revenue model and audience. Always include two cross-cutting lanes: **Personal Brand** (positioning and the through-line across lanes; a multiplier, not a direct earner) and **Life/Ops** (time, energy, sustainability). Cross-cutting lanes never headline a panel; they attach seats to other lanes' questions. Each lane gets: name, description, platforms, revenue model(s), status (active / experimental / paused), one KPI, default bench.
3. **Goal.** The number and deadline (e.g. "$10K creator revenue by Dec 31"), plus how progress is measured per lane.
4. **Roster.** Show `advisors-library.md` (bundled with this skill) ranked per lane. User confirms core/bench/cut, adds their own names. For any new name, research them live (web search): who they are, thinking profile, biases, lane tags, and an emulation-quality grade (deep / good / thin). Flag thin-footprint names into the watchlist rather than seating them.
5. **Write the instance.** Create the folder (from the user's project template if they have one):

```
PROJECTS/board-of-advisors/
  INDEX.md, CHANGELOG.md
  00-inputs/context.md      # identity, revenue, assets, constraints, goal
  00-inputs/lanes.md        # lane definitions
  00-inputs/advisors.md     # personalized roster, tagged; watchlist at bottom
  01-working/
  02-outputs/STATUS.md      # goal progress, open decisions
  02-outputs/decision-log.md
  02-outputs/decisions/
  02-outputs/checkins/
  _versions/
```

decision-log.md header row:
`| date | question | lanes | panel | verdict | final votes | predicted $ | actual outcome | reviewed on |`

## Mode 2: /board [question] — deliberation

### Step 1: Intake
Read `context.md`, `lanes.md`, `advisors.md`, and the last ~10 rows of `decision-log.md`. If the question is thin, ask up to 3 clarifiers in one batch (stakes, options already considered, deadline). "Just run it" skips clarifiers. Compress everything into a brief paragraph pasted into every advisor prompt.

### Step 2: Chairperson seats the panel
Classify the question by lane(s) and decision type. Seat 5-7:

- **Money seat (always).** Match to the revenue type in play: offers/pricing, sponsorships, digital products, or unit-economics reality check.
- **Contrarian seat (always).** The advisor whose KNOWN BIASES run against the idea (e.g. an AI-skeptic on an AI-music question). Their mandate: attack it from within their real worldview.
- **Audience seat (always).** One advisor argues as the target viewer/buyer, first person, skeptical.
- **2-4 domain seats** from the relevant lane benches. Multi-lane questions draw from each lane.
- **Cross-cutting attachments.** Personal Brand seat joins anything affecting how the user is perceived across lanes. Life/Ops seat joins anything involving significant hours or burnout risk.

State the seating and one-line reasons BEFORE deliberating, and accept overrides: "add X", "drop Y", "[lane] bench only", or "full board" (9-11 seats, for big decisions). Then proceed.

### Step 3: Round 1 (parallel agents)
Spin up all seated advisors in parallel (one general-purpose agent each, single message). Each prompt contains: the brief, the advisor's profile + biases from advisors.md, their seat mandate, and relevant decision-log history. Each returns 300-600 words in character: their position, reasoning from their actual public frameworks, a **YES / NO / CONDITIONAL** vote (CONDITIONAL must name the condition), and committed numbers: est. cost, est. hours, expected revenue range, time to first dollar, confidence (low/med/high). The Researcher-type and evidence-heavy advisors may use web search; pure-framework advisors reason from principles.

### Step 4: Round 2 (parallel agents)
Send every advisor ALL Round 1 positions. Each returns 150-400 words: who they disagree with most and why (quoting the actual argument), whether anything changed their mind, and a **final vote**.

### Step 5: Judge synthesis (you, the main session — not an agent)
Do not average votes. Name the central tension and resolve it. Output in chat, skimmable:

```
## BOARD VERDICT: GO / RESHAPE / KILL / SPLIT-TEST
Confidence: low / medium / high

**The call:** [one line]
**Why:** [2-3 sentences resolving the tension]

**Votes:** [advisor: R1 → final, flag changes with →]
**Biggest fight:** [who vs who, over what]
**Biggest risk:** / **Biggest upside:**
**Money read:** [realistic revenue range, time to first dollar, hours — labeled estimates]
**Cheapest 48-hour test:** [smallest thing to validate the riskiest assumption]
**Next action:** [one thing, this week]
```

SPLIT-TEST = the board is genuinely split and both paths are cheap to test; define both tests.
The Judge may also rule "take this to /roast first" if the idea is raw and unvalidated.

### Step 6: Write-up
Save the full record (seating, both rounds, synthesis) to `02-outputs/decisions/yyyy-mm-dd-slug/decision.md`. Append one row to decision-log.md (leave `actual outcome` and `reviewed on` empty). Update STATUS.md, INDEX.md, CHANGELOG.md. Chat shows the synthesis only.

On request ("full package"), additionally build an interactive HTML dashboard for the deliberation (assumption sliders recalculating projections, vote-change visuals, advisor cards).

## Mode 3: /board checkin

1. Read STATUS.md, decision-log.md, lanes.md, and the most recent file in `checkins/`.
2. Ask the user 3 quick questions in one batch: what shipped since last time, what stalled, any revenue events (amounts and sources).
3. Seat a review panel: money seat + Life/Ops seat + one advisor per lane that was active this period (per the user's answers and the log).
4. Panel produces (parallel agents, one round, then you synthesize):
   - **Pace math**: progress vs the goal, on-track or not, required run-rate for the remaining months.
   - **Decision outcomes**: for every log row with an empty `actual outcome`, ask/infer what happened and fill it in, with `reviewed on` date.
   - **Advisor accuracy**: compare final votes to filled outcomes; update the accuracy note on each advisor in advisors.md (e.g. "3-for-4 on product calls").
   - **Lane review**: any lane earning attention it isn't getting, or eating time it shouldn't; recommend status changes (active/experimental/paused).
   - **The one decision** to bring to the board next, phrased as a /board question.
5. Save `checkins/yyyy-mm-dd-checkin.md`, update STATUS.md, decision-log.md, CHANGELOG.md. Chat gets a short summary: pace, one insight, the next decision.

If running from a scheduled session and the user's board folder is unreachable, send a brief nudge to open Cowork and run `/board checkin` instead; do not fabricate a check-in.

## Mode 4: /board roster

Show the roster grouped by lane with seat types and accuracy notes. Handle: add (live research → profile with lane tags, biases, emulation grade; thin footprints go to the watchlist with the reason), bench, unbench, cut. Version advisors.md before structural changes.

## Mode 5: /board log

Render decision-log.md as a readable table plus: verdict counts, outcome hit rate, and per-advisor accuracy summary. No agents needed.

## Advisor profile format (advisors.md and the bundled library)

Per advisor:
- **Name** (lane tags) — seat types: money / contrarian / audience / domain / life
- Thinking profile: 2-3 sentences on how they think and what they prioritize.
- Known biases: used to argue in character AND to cast the contrarian seat against ideas.
- Emulation grade: deep / good / thin.
- Accuracy note: maintained by check-ins; starts empty.

## Rules

- Advisors stay in character and do not hedge. The value is the friction.
- The Judge makes an actual call. "It depends" is not a verdict.
- Never present emulated advisors as the real person's endorsement.
- All money figures are labeled estimates; the Judge sanity-checks the math.
- Keep chat output skimmable; depth lives in the saved files.
- Respect the user's file conventions (naming, versioning, changelogs) if they have them.

---

## For new users (if you received this skill from someone)

`/board setup` interviews you and builds your own private board folder: your lanes, your goal, your roster (start from the bundled `advisors-library.md`, then make it yours). Then bring it any real decision: `/board should I launch a paid community?` For a recurring check-in, ask Claude to schedule `/board checkin` every other week.
