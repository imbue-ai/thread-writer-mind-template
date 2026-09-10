<p align="center">
  <img alt="Thread Writer" src="template.svg" width="480">
</p>

# Thread Writer

<p align="center">
  <a href="https://boweiliu.github.io/open-in-minds/?git_url=https://github.com/MINDS_TEMPLATE_REPO_URL"><img alt="Open in Minds" height="64" src="https://img.shields.io/badge/Open%20in%20Minds-D8D1C0?style=for-the-badge"></a>
</p>

Didn't work? Create a Minds workspace and paste this to your agent:
` /use-template https://github.com/MINDS_TEMPLATE_REPO_URL`

## Why you care

A multi-voice, multi-format generator that turns blog posts, links, or bullet points into X threads and LinkedIn posts, with a schedule queue and a cached voice-corpus mechanism.

Writing didn't stop when you hit publish -- someone still has to turn a blog
post, a trending link, or a handful of notes into a tweet, a thread, or a
LinkedIn post, in a voice that doesn't sound like a press release. Thread
Writer does that draft for you, in three different voices, and never posts
anything without you reviewing it first.

## How to use it

1. Open the Schedule view and paste in a source: a link to a trending
   article, a few bullet points, or let it pick up your own blog's latest
   posts automatically.
2. Open the reader for that post. Pick a **voice** (Normies, Researchers, or
   Quotes) and a **format** (Tweet, Thread, or LinkedIn post) and hit
   Generate. Switch voice or format any time -- each combination generates
   and saves independently, and Regenerate reruns just the one you're
   looking at.
3. Read the draft as a stack of tweet cards with live character counts. When
   it looks right, hit Publish -- this copies the text to your clipboard and
   opens X's or LinkedIn's own composer so you paste, review, and send it
   yourself. Nothing is ever posted automatically.
4. Back in the Schedule view, the "Up next" queue shows what's due with a
   recommended, staggered date, and a month calendar shows what's already
   gone out.

## Ideas for making it yours

- Add a fourth voice for a different register you write in (a press-release
  voice, a technical-deep-dive voice, whatever fits your brand).
- Add a fourth format -- a Threads (Meta) post, a newsletter blurb, a
  Mastodon toot -- alongside Tweet / Thread / LinkedIn.
- Feed the Schedule queue from an RSS feed instead of (or alongside) your own
  blog listing, so threads get suggested the moment any source you follow
  publishes something new.
- Build your own voice corpus from your team's Slack, newsletter archive, or
  past social posts, so drafts are grounded in real examples of how your team
  actually writes (see "Requirements" in `template.md` for how).
- Add a lightweight approval step -- Slack-notify a teammate when a new draft
  is ready, before it shows up in the "Up next" queue.

## What this is

This repository is a published **minds template**: a clean, bootable
snapshot of what a mind built, ready to adapt into your own. It is NOT the
generic workspace template -- it is this specific project.

[`template.md`](template.md) is the full manifest -- what it is, how it
works, what it needs to run, and what to adapt -- with the
machine-readable half (recipe, requirements, and the environment it needs
installed) in [`template.toml`](template.toml).
