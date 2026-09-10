---
title: "Thread Writer"
description: "A multi-voice, multi-format generator that turns blog posts, links, or bullet points into X threads and LinkedIn posts, with a schedule queue and a cached voice-corpus mechanism."
thumbnail: "template.svg"
version: v1
format: v2
---

# Thread Writer

This file is the manifest for the **Thread Writer** template (slug:
`thread-writer`). It is the one document a future agent reads to understand,
present, and adapt this template. If you are an agent in a mind that was
created from this template, this file is your script: read all of it, then
follow "How to adapt it" below.

## What it is

A multi-voice, multi-format generator that turns blog posts, links, or bullet points into X threads and LinkedIn posts, with a schedule queue and a cached voice-corpus mechanism.

Thread Writer solves the "we published something, now someone has to turn it
into social posts" problem. Paste a source -- one of your own blog posts, any
trending link, or a few bullet points -- and it drafts ready-to-post social
copy from it. Every draft can be written in one of three **voices** (Normies,
Researchers, or Quotes) and one of three **formats** (a single Tweet, a Thread
of tweets, or a long-form LinkedIn post), and any voice-and-format combination
can be generated or regenerated on demand with one click. Running it, the user
sees two surfaces: a **reader** that renders a chosen draft as a stack of
tweet cards with live character counts, a voice dropdown, a format tab
toggle, and a Publish button; and a **Schedule** view with an "Up next" queue
of what is due to post (each with a recommended, staggered date), a month
calendar of published posts, and paste-a-link / paste-your-notes inputs.
Publishing never auto-posts -- it copies the text to the clipboard and opens
X's or LinkedIn's composer so the user reviews and sends it themselves.

## How it works

The snapshot includes these paths (each is a repo-root-relative path copied
from the original mind onto a clean default-workspace-template base):

- `system/apps/thread_writer`
- `system/supervisord.conf`
- `pyproject.toml`

**`system/apps/thread_writer`** is the whole app: a single-file Flask service
(`src/thread_writer/runner.py`) plus its `README.md` and `pyproject.toml`. The
one module holds everything -- the HTML/CSS/JS for both the reader and the
Schedule pages (rendered as f-strings, no template engine, no separate
frontend build), the blog/link/YouTube fetching and parsing, the on-demand
generation pipeline, and the small JSON registries that persist state.

**`system/supervisord.conf`** runs the app. The `[program:thread-writer]`
stanza first runs `system/scripts/forward_port.py` (with the app's own icon)
to register the service so it shows up as a tab in the workspace UI, then
launches `uv run thread-writer` (the console script defined in the app's own
`pyproject.toml`, which calls `runner:main`). The whole command is wrapped in
`system/services/oom_priority/bin/oom_tag_service.py user` so the
OOM-prevention daemon treats it as a sheddable user service. `main()` serves
the Flask app on `127.0.0.1` with the threaded Werkzeug server -- threaded
because generation calls are synchronous and can take ~10-30s, so the server
must absorb the wait without blocking other requests. Port and data directory
are overridable via `THREAD_WRITER_PORT` and `THREAD_WRITER_DATA_DIR`.

**`pyproject.toml`** (the workspace root) carries `thread-writer` as a
workspace member and dependency so `uv run thread-writer` resolves.

At runtime everything hangs off `DATA_DIR` (defaults to
`data/.apps/thread-writer/`): generated drafts live at
`DATA_DIR/threads/<slug>/<voice>.<format>.json` (one file per voice+format,
all the same JSON shape, a LinkedIn post being a single-element `tweets`
array); pasted links are recorded in `DATA_DIR/trending.json`; pasted notes in
`DATA_DIR/drafts.json`; mark-as-posted / scheduling state in
`DATA_DIR/schedule_state.json`; and, if present, the per-voice example
corpora are read from `DATA_DIR/voices/*.md` (see "Requirements" below --
none ship with this template). Generation (`/generate`, `/trending`,
`/draft`) fetches or reads the source text, builds a voice- and
format-specific prompt grounded only in that text, calls the model, parses
strict JSON back into tweet strings, and writes the per-post file; the reader
then renders it.

## Recipe

This template is version `v1`. It is not a fork of the
workspace it came from -- it is DERIVED from it by a recipe: include these
paths, leave these out, apply these published-version rules. An update re-runs
the recipe against the current workspace and publishes the result as the next
version, so anything excluded stays excluded even though it still exists in the
source workspace.

The recipe is machine-read, so it lives in the sibling
[`template.toml`](template.toml) -- its `[recipe]` table -- along with
the structured requirements and the environment this template needs
installed. That file is authoritative for all of it; this one holds the prose.

## Requirements

Everything the adopting mind must deal with before this template is really
theirs. Two kinds of entry, handled at different times:

- **Activation** -- what must be SET UP before anything runs, in the
  machine-readable `requires_` forms below. The adopting agent acts on these
  ITSELF, first, before asking anything.
- **Adaptation** -- what must be DECIDED or REWIRED, in prose. Worked through
  interactively with the user, after activation.

**Activation**

- requires_llm: calls Claude for generation via the KEYED litellm path
  (`litellm.completion`, reading `ANTHROPIC_API_KEY` and `ANTHROPIC_BASE_URL`
  directly from the process environment), using model
  `anthropic/claude-fable-5` as the primary writer with
  `anthropic/claude-opus-4-8` as the fallback. This is the only hard
  requirement to produce any draft. An adopter whose mind uses the keyless
  subscription path (`claude -p`) must switch the model calls in
  `_complete_generation` (`runner.py`) to `claude_p_completion` per the
  use-ai-integration skill -- this is a code change, not config.

Blog/link fetching needs no special auth (a plain public HTTP GET), and
"Publish" never posts anything -- it only copies text and opens a composer in
the browser -- so neither needs any permission or secret.

**Adaptation**

This app is **Imbue-specific by design**: the original author intentionally
did not generalize it into config, so adapting it means editing the code, not
flipping switches.

- The blog source is hardcoded to imbue.com (`BLOG_URL` and
  `_parse_blog_posts`'s markup selectors target imbue.com's exact HTML). An
  adopter with their own blog points it there and rewrites the parser to
  match their listing's markup -- or, if they only ever use the paste-a-link
  and paste-notes inputs, they can ignore the blog listing entirely; those
  two paths work with no blog at all.
- Threads generated from the adopter's own blog close on Imbue's mission
  (`_close_directive`: "Imbue builds software that is open source, runs on
  your own device, and that you own" plus a CTA). An adopter edits that
  closing sentence to their own mission/CTA (the external-link and notes
  closes are already neutral and need no change).
- The voice presets and guidance are tuned to Imbue (`VOICE_PRESETS`,
  `_VOICE_GUIDANCE`): "Normies" names swyx and the Imbue Slack, "Researchers"
  names Andrew Ng and Yann LeCun. An adopter edits these to describe their own
  target registers.
- The per-voice example corpora are not shipped -- see "Environment" below.
  Generation still works out of the box via the built-in inline guidance;
  an adopter who wants the richer "grounded in real writing" effect drops
  their own markdown corpus files under `DATA_DIR/voices/` (file names come
  from `_VOICE_CORPUS_FILES` in `runner.py`).
- The Schedule "revive" queue's YouTube half is hardcoded to Imbue's channel
  (`YOUTUBE_HANDLE_URL`). An adopter points it at their own channel handle, or
  ignores it (the blog half of the queue is independent).

## Environment

What this template needs INSTALLED, beyond what the template already has.
Declared in `template.toml`'s `[environment]` table; an adopting mind
converges it at ITS OWN pinned apt snapshot timestamp, so package versions come
out consistent with the rest of that mind's environment rather than frozen to
whatever this publisher happened to have.

Nothing extra -- runs on the stock workspace environment. All of the app's
Python dependencies (Flask, httpx, beautifulsoup4, litellm, etc.) are declared
in `system/apps/thread_writer/pyproject.toml` and resolve through the normal
`uv sync --all-packages`; there is no apt package, no global npm/uv/cargo
tool, and no `env.d` unit to install.

One thing worth knowing even though it is not an installable dependency: the
per-voice example corpora this app can use to ground its writing voice are
**not** shipped with this template (see "Requirements" above). Out of the box
every voice falls back to its built-in inline guidance; the corpora are
opt-in data an adopter supplies themselves.

## How to adapt it

Instructions for the NEXT agent -- the one adapting this template into a
new mind. This is the `use-template` skill's template path; in short:

1. Read this entire file first, especially "Requirements" below. It holds two
   kinds of entry and they are handled at different times: the machine-readable
   `requires_` lines are ACTIVATION (set them up before anything runs), and
   the prose bullets are ADAPTATION (decide or rewire them afterwards).
2. Present the template to the user in plain, non-technical language: what
   it is, what it does, and what it needs from them (name the activation
   requirements).
3. Ask whether they want to use the same connectors (e.g. their own Slack).
   If YES: ACTIVATE FIRST -- initiate every `requires_permission` line NOW
   via a latchkey permission request (see the `latchkey` skill; the request
   opens the approval/login flow in the minds app), wire up any
   `requires_secret` values, start the services, and get the app showing
   THE USER'S OWN DATA. Done for a data-backed app means the user can open it
   and see their own data -- NOT that a service starts or an endpoint returns
   200. Then tell them it is live and to take a look.
4. Only AFTER that (or immediately, if they chose different connectors -- the
   swap is then the first adaptation) ask: "How do you want to adapt it?"
5. Work through each requirement interactively, one at a time. Translate each
   into plain language, ask for a decision only when you genuinely need one,
   and resolve the obvious ones yourself.
6. When done, append a dated entry to "Adaptation history" below (never
   rewrite earlier entries) and commit.

## Publication history

This template's changelog: what each published version changed. The PUBLISHER
appends one entry per version (newest last); earlier entries are never rewritten.
This is distinct from "Adaptation history" below, which is the ADOPTERS' log.

### v1 (2026-09-10) -- Initial publish of the Thread Writer app: multi-voice
(Normies / Researchers / Quotes) x multi-format (Tweet / Thread / LinkedIn)
generation from blog posts, pasted links, or notes, with a Schedule queue,
calendar, on-demand Generate/Regenerate, and copy-and-open Publish. Re-cut
from `jean-imbue/thread-writer` (pinned at `minds-v0.3.9`) onto this mind's
current base.

## Adaptation history

Each mind that adapts this template appends one dated entry below. Earlier
entries are never rewritten.
