# Build Your Agent — Facilitator & Participant Guide

**IEEE SMC · MIT Bengaluru · Technical Symposium · 5 Sep 2026**

This is the companion guide for `build-your-agent.ipynb`. It explains what
each section does, why it's there, and what to say if you're running this
live in the room. Total time: 10–12 minutes if people follow along, less
if you demo it and let people take the notebook home.

---

## Before the room fills up

1. Open [aistudio.google.com/apikey](https://aistudio.google.com/apikey) yourself and confirm key creation still works — it's a Google product, it moves.
2. Open the notebook in Colab once, run every cell top to bottom with your own key, so you know it works on the day, not just when you wrote it.
3. Have the notebook link ready to paste in chat/on screen. Two ways to hand it out:
   - **Colab, one click:** upload the `.ipynb` to a public GitHub repo, then share it as `https://colab.research.google.com/github/<user>/<repo>/blob/main/build-your-agent.ipynb` — this opens directly in Colab for anyone, no download needed.
   - **GitHub, view only:** just the raw GitHub link — GitHub renders `.ipynb` files natively, so people can read it even before they run it.

---

## What to say at the start

> "Everything you just clicked through in the browser demo was scripted — the trace was written in advance, it always ends the same way. This notebook is the same eight patterns, wired to a real model. If your tool description is bad, it will actually misfire in front of you. That's not a bug in the demo — that's the whole point."

---

## Section-by-section

### Step 0 — Get a key
Free, no credit card, from Google AI Studio. This is the only manual step —
everything else is "run the cell."

**If someone's key doesn't work:** the free tier does have a daily request
cap. It resets every 24 hours and won't be hit by anything in this notebook
unless someone re-runs it a dozen times. If a cell throws a quota error,
that's the actual answer — not a bug to debug live.

### 1 — A plain call → mirrors **Campus Agent Lab, Stage 01**
Ask the model something it cannot know (a real calendar it has no access to).
It will answer anyway, fluently, and be wrong. This is deliberate — it's the
same "will not throw an exception, it will lie politely" moment from the
Basic LLM stage, except this time nobody scripted the wrong answer. The
model produced it live.

**Talking point:** ask the room what the model said. It's never the same
answer twice, but it's always confident.

### 2 — Give it tools → mirrors **Campus Agent Lab, Stage 03**
Two toy Python functions — `get_timetable()` and `check_conflict()` — get
handed to the model as real callable tools. Ask about Saturday, and the
model calls the tools before answering instead of guessing.

Then the notebook asks about **Tuesday at 7pm** — the same clash from the
bridge deck's evaluator slide. Sometimes the model catches its own conflict
in one shot; sometimes it doesn't. Both outcomes are realistic, and both
make the point for section 3: **a working tool call is not the same as a
correct decision.**

### 3 — Catch it lying → mirrors **Campus Agent Lab, Stage 07**
A plain Python function checks the proposed booking against the real
timetable — no model call, just an `if` statement. If it fails, the failure
gets fed straight back to the model as the next turn, and it has to try
again with the correction in hand.

**This is the one that lands hardest.** It's the literal difference between
an evaluator that logs a failure and one that's actually wired into the
loop — the exact gap from the Hugging Face field report in the deck, where
the alarm fired and nobody was listening.

### 4 — Add a rail → mirrors **Campus Agent Lab, Stage 08**
One `input()` call in front of the write action (`register_workshop`).
That's it — that's the whole pattern. The point was never that approval
gates are technically hard to build; it's that people forget to put one in
front of the specific function that can't be undone.

### 5 — Capstone → mirrors **Life Agent Builder**
Same five patterns from the browser tool's Phase 1 (2am scroll, 11pm cart,
9-tab explosion, unanswered reply, forgotten deadline). Four functions to
fill in — `sense()`, `decide()`, `act()`, `rail()` — and `decide()` is the
only one that calls the model. Everything else is ordinary code, same as
the real architecture underneath every agent in this room.

**This is the deliverable.** When someone's rail actually blocks a bad run,
or their agent produces a genuinely useful nudge, that's the "then you
build one" moment paid off in real code instead of a diagram.

### 6 — Take it further → mirrors **Campus Agent Lab, Stages 02, 04, 05, 06**
The notebook builds four of the eight stages live because together they're
a complete arc in ten minutes — fails on its own, gets tools, gets caught
lying, gets gated. The other four (Router, Planner, Multi-Agent, Memory)
are just as real, but each needs its own bit of scaffolding, and building
all eight would have doubled the session length.

Section 6 gives skeleton code for all four, reusing everything already set
up earlier in the notebook (`client`, `MODEL`, `tools`, `available_functions`)
— nothing new to install or configure, just new code to fill in:

- **6a Router** — classify a request into Academic/Timetable/Events/Admin
  before acting, two ways (keyword rule vs. one small model call)
- **6b Planner** — ask the model for an ordered JSON plan of tool calls,
  then execute the plan yourself, in order
- **6c Multi-Agent** — two narrow specialists plus a coordinator; the
  scaffold deliberately has each specialist read the *original* user
  request, not a peer agent's paraphrase — the trust-boundary point from
  the Hugging Face field report
- **6d Memory** — the simplest of the four; a plain dict that gets read
  back into the next prompt, proving a preference survives across calls
  without the model being told twice

**Talking point if you're facilitating live:** point people at Section 6 as
a take-home, don't try to run it in the room. If someone finishes the
capstone early, 6d (Memory) is the fastest one to actually complete on the
spot.

---

## If something breaks live

- **Model name 404s:** the notebook has a fallback cell that lists every
  model your key can actually use — run it, copy a working name into the
  `MODEL` variable near the top, re-run from there.
- **`previous_interaction_id` errors in section 3:** the retry function
  already catches this and falls back to a fresh call. If you see a
  different error there, skip to section 4 and come back — nothing after
  it depends on section 3 succeeding.
- **Someone's Colab secret isn't found:** the key-loading cell falls back
  to a hidden `getpass` prompt automatically — tell them to just paste the
  key when asked.

---

## After the session

Point people back to:
- `campus-agent-lab.html` — the visual version of stages 1–10
- `life-agent-builder.html` — the visual version of the capstone
- `pradyoth-bridge-deck.html` — the case studies referenced throughout (Air Canada, Chevrolet, the Hugging Face incident)

All three are live at `mit-symposium-demo-2026.web.app`.
