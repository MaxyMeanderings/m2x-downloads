# The m2x plugin for Claude Code

One install gives Claude Code everything it needs to drive m2x:

- **`/m2x:map`** — the read direction: scan a project, read `diagrams/map.json`,
  brief the owner in plain language, annotate the live canvas.
- **`/m2x:scribe`** — the write direction: interview the owner, put what they
  *mean* on the record (`diagrams/intent.json`) in pencil, before anything is
  built.
- **The pointer** (a `UserPromptSubmit` hook) — when an `m2x serve` canvas is
  open, whatever the owner has selected rides into every prompt. Click the
  Twilio box, type "what is this?" — the answer is about Twilio. Stale or
  empty selections stay silent.

## Install

Requires the **`m2x` binary ≥ v0.4.0** on PATH first (download the release, run
it once — it self-installs — or `m2x install`). v0.4.0 is where the `selection`
verb the hook calls landed; with an older binary the hook **fails soft** (it
can't block your prompts — m2x never returns the blocking exit code), but
update to silence the stderr noise. Then, inside Claude Code:

```
/plugin marketplace add MaxyMeanderings/m2x
/plugin install m2x@m2x
```

This replaces the older "paste one line into CLAUDE.md" setup — the skills
carry the instructions now.
