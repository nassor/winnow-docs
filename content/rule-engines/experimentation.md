+++
title = "Feature flags & experimentation"
description = "Decide which users get which feature or variant: one user against every rule, on the request path."
weight = 80

[extra]
theme = "growth"
+++

Shipping software safely means controlling *who* sees *what*: roll a feature out to
1% of users, target a beta to one region, run an A/B test, and kill a flag instantly
if something breaks. Each of those is a targeting rule evaluated on every request.

<div class="callout callout-perf">
<p class="callout-title">⚡ Where speed pays for itself</p>
<p>Flag and experiment checks happen on every request, often dozens per page. A flag tool that adds delay or a network call per check taxes the entire application. Winnow decides in microseconds, inside your own service, which makes the cost of any number of flags negligible. Teams can run thousands of concurrent experiments without slowing the product or adding servers to carry the load.</p>
</div>

## The stakes

Experimentation is how the best product organizations decide what to ship, and they
do it at scale: Microsoft, Amazon, Booking.com, Google, and others each run **more
than 10,000 online controlled experiments a year**, with Booking.com running **over
1,000 concurrently** at any moment
([HBR](https://hbr.org/2017/09/the-surprising-power-of-online-experiments)). It is
humbling work: even in mature products, **most ideas fail to improve the metric they
target**, which is exactly why teams test rather than guess. That cadence only works
if flag and experiment targeting is cheap to evaluate and instant to change.

## How it works

A flag's targeting is a rule over user and context attributes. For example,
*"enable for app version 12.0+ (stored ×100), in the US (1) or UK (44), for 10% of
users (bucket 0–9 of 100), excluding internal accounts."* On each request, the
current user is matched against every flag and experiment rule: a
reverse query returning the variants that apply.

<div class="callout callout-ai">
<p class="callout-title">🤖 With AI in the loop</p>
<p>Feature gates are how AI itself ships safely. <a href="https://www.statsig.com/">OpenAI</a>
rolled out ChatGPT features behind Statsig feature gates (rules deciding per
user which model variant runs, scaling to hundreds of experiments across
hundreds of millions of users), with an instant flag flip as the rollback plan
(OpenAI has since acquired Statsig). The same shape governs any model rollout:
champion/challenger routing, shadow traffic, and percentage ramps are all
targeting rules evaluated per request. In Winnow that evaluation costs
microseconds and sits inside your own service, so gating a new model on 10% of
users in one region adds nothing you can measure, and killing a misbehaving
variant is one rule edit, applied live. More
patterns: <a href="../ai-and-rules-engines/">Deep Dive: AI + Rules Engines</a>.</p>
</div>

## Why Winnow fits

- **Attributes are native:** version and percentage buckets are
  number checks; country, platform, and plan are
  category checks; rollout logic combines
  with `AND` / `OR` / `NOT`.
- **On the request path:** one user evaluated against the whole flag set in
  microseconds, inside your own service. No call to a flag service per request.
- **Instant kill switch:** flip or retarget a flag by editing a rule; the change
  applies live with no redeploy, which is the safety
  property feature flags exist to provide.
- **Thousands of concurrent flags** evaluate cheaply because the whole flag set is
  checked in one pass, not flag by flag.
