+++
title = "Content moderation & abuse filtering"
description = "Apply trust-and-safety rules to a high-velocity stream of user content: text-first, at scale."
weight = 100

[extra]
theme = "ops"
+++

Every platform that accepts user content needs a first line of defense: rules that
catch spam, abuse, and policy violations automatically, so human reviewers can focus
on the genuinely nuanced cases. Most of that first pass is text matching, Winnow's
strong suit.

<div class="callout callout-perf">
<p class="callout-title">⚡ Where speed pays for itself</p>
<p>The cost driver is sheer volume: text matched against policy across a relentless content stream. Fast text matching lets automated rules clear the high-volume, routine cases cheaply, so scarce (and expensive) human reviewers see only the genuinely ambiguous residue. Faster matching means more content auto-handled per server and fewer hours sent to manual review.</p>
</div>

## The stakes

The volume is the whole problem. **More than 500 hours of video are uploaded to
YouTube every minute**, about 720,000 hours a day
([Statista](https://www.statista.com/statistics/259477/hours-of-video-uploaded-to-youtube-every-minute/)),
and text posts dwarf even that. Manual review cannot scale to that velocity;
automated rules have to filter the routine, high-volume cases so that scarce human
attention goes where it is actually needed.

## How it works

Policy checks combine text matching with behavioral signals, for example, *"flag
content that contains a prohibited phrase, or carries many links from an account
less than two days old, or has already drawn three user reports."* Phrases
are found with fast text search; link counts
and account age are number checks; the conditions combine
with plain and/or/not logic.

<div class="callout callout-ai">
<p class="callout-title">🤖 With AI in the loop</p>
<p>Moderation at scale is classifiers plus policy rules. <a href="https://transparency.meta.com/enforcement/detecting-violations/technology-detects-violations/">Meta's</a>
systems find more than 90% of the content it removes before anyone reports it;
high-confidence detections are removed automatically while lower-confidence ones
route to human reviewers. <a href="https://newsroom.tiktok.com/en-us/advancing-our-approach-to-user-safety">TikTok</a>
scopes what its AI may do by explicit rule: automatic removal is reserved for
the categories where accuracy is highest, keeping the false-positive rate for
automated removals around 5%. And <a href="https://developers.openai.com/api/docs/guides/moderation">OpenAI's moderation models</a>
deliberately return 0–1 scores per harm category, leaving each product's own
policy rules to decide what to filter, review, or escalate. In Winnow those
classifier scores are columns; the per-market thresholds and routing that turn
them into actions are rules a trust-and-safety team edits live. More patterns:
<a href="../ai-and-rules-engines/">Deep Dive: AI + Rules Engines</a>.</p>
</div>

## Why Winnow fits

- **Text-first matching:** finding words and phrases inside content is native:
  indexed search for the standing rulebook,
  plus a direct scanner for short or just-added
  terms, exactly what banned-phrase and spam rules need.
- **Built for volume:** the whole rulebook is checked in one pass, in microseconds
  per item, keeping pace with a real-time content stream.
- **Layered signals:** combine text rules with behavioral thresholds (`link_count`,
  `account_age_days`, `report_count`) so a rule fires on the *pattern*, not a single
  keyword, meaning fewer false strikes on legitimate posts.
- **Policies change weekly:** rules are plain text, so
  trust-and-safety teams add and adjust them continuously, applied live.
