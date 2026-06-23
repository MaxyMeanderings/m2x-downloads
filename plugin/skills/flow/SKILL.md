---
name: flow
description: Build "how it works" as a data-flow story the owner can follow. Use this whenever the user wants to understand how their product works, how their data moves, where it goes, what it costs, or what's at risk ("how does this work?", "walk me through it", "where does my data go?", "trace the flow", "explain how a booking works"). Produces the flow record m2x renders as a story panel beside the map. This is the interpretive half of the flow runtime; m2x supplies the deterministic skeleton, you tell the story.
---

# m2x flow — tell how it works, as a story the owner can follow

You are building the **flow**: the data's journey through the system — what
moves, from where, to where it leaves (a third party) or rests (a store) — told
as a story for **the owner: a domain expert who directed the build but doesn't
read code**. The purpose is *enablement*, not a readout: when they're done, they
should feel they can keep **directing** the build. Director, not passenger.

You do not freehand this. m2x gives you a deterministic **skeleton**; you fill a
fixed schema on top of it, and a verb validates your work. That's what keeps the
story consistent and honest (docs/flow-runtime-spec.md).

## The procedure (follow it in order)

1. **Get the skeleton.** `m2x flow skeleton <folder>` → JSON: the typed nodes
   (ingress / process / egress / store / gate) and the **grounded** edges m2x
   already proved (file→file imports; file→service via a shared dependency;
   secret→file env reads). This is your whole vocabulary — **never invent a
   node.**

2. **Write `flow.json`.** Two parts:
   - `edges` — for each connection that carries data, one entry:
     `{ id, from, to, carries, kind, sensitivity, provenance }`.
     - `from`/`to` are skeleton node ids (only).
     - `carries` is the data **in the owner's words**: "the customer's card",
       "their phone number" — never "a string" or "the payload".
     - `kind`: reads | writes | calls | sends | receives.
     - `sensitivity`: none | personal | payment | secret.
     - `provenance`: `grounded:<note>` when the skeleton proved the connection;
       `inference:<basis>` when you're reading between the lines (data passed at
       runtime the imports don't show). **Inferences are never silent** — state
       the basis, and say them out loud to the user so they can confirm.
   - `journeys` — the story. Each is a named, ordered walk:
     `{ trigger, steps: [{ edge, say }], lever: { say, anchor } }`.
     - `trigger`: what starts it — "a customer books", "someone signs up".
     - each `step.edge` must reference an edge you defined above; `say` is one
       plain line in the owner's words.
     - `lever`: the **director hand-off** — end every journey by naming the part
       they'd point an agent at to change this (`anchor` = a skeleton node id).

3. **Validate + store.** `m2x flow add <flow.json> <folder>` validates against
   the skeleton and writes `diagrams/flow.json`. If it rejects something, fix it
   — don't fight the gate; it's catching a node you invented, a journey step
   that walks an edge you never defined, or a "grounded" claim the skeleton
   can't back. (`m2x flow validate <flow.json>` checks without storing.)

4. **Show them.** Open `m2x serve <folder>` — the flow renders as the **story
   panel** beside the map: the journey top-to-bottom, each step a line, click a
   step to light up the part it touches, and the lever at the end. Read it to
   them in plain language; surface every inference for a yes.

## Rules

- m2x never calls a model — you are the story layer. The skeleton is ground
  truth; cite it, don't embellish past it.
- The owner's words, never stack jargon. Gloss anything technical.
- Cost and risk fall out of the flow: a metered `egress` is a per-use bill;
  `payment`/`personal`/`secret` sensitivity is what to handle with care. Weave
  those in, but flags are "worth knowing," never verdicts.
- If the skeleton changed since a stored flow (m2x will tell you it's stale),
  re-derive and re-propose — don't narrate a flow the code has outgrown.
