+++
title = "Compliance, AML & watchlist screening"
description = "Screen transactions and customers against regulatory rules and watchlists, with the precision to avoid drowning analysts in false alarms."
weight = 20

[extra]
theme = "risk"
+++

Anti-money-laundering and sanctions programs are rule-driven by law: every
transaction and customer must be checked against thresholds, typologies, and
watchlists. The hard part is doing it *precisely*: flagging real risk without
burying a compliance team in noise.

## The stakes

Rules-based monitoring is notorious for false alarms: AML false-positive rates
typically run **85–95%**, and global AML compliance is estimated to cost the
industry **over $274 billion a year**, much of it spent investigating
low-quality alerts ([Facctum](https://www.facctum.com/blog/aml-false-positive-report)).
Each alert can take hours to clear. Better-targeted rules (combining several
conditions instead of a single blunt threshold) are the most direct lever on that
cost.

## How it works

Each typology or watchlist check is a rule, evaluated against every transaction or
customer record. For example, *"flag a transfer of $10,000+ (amount in cents) to a
higher-risk jurisdiction where the payee name contains a watchlisted term, unless
the customer is already in the lowest-risk band."* Name screening is a text match;
amounts and velocity are number checks; jurisdictions and
product types are category checks.

<div class="callout callout-ai">
<p class="callout-title">🤖 With AI in the loop</p>
<p>Modern AML programs pair machine learning with an explicit rule layer; they
need both. <a href="https://cloud.google.com/blog/topics/financial-services/how-hsbc-fights-money-launderers-with-artificial-intelligence">HSBC</a>,
screening over 1.2 billion transactions a month with Google Cloud's AML AI,
found <strong>2–4× more suspicious activity with 60% fewer alerts</strong>. The
model scores every customer, and a threshold policy sized to investigator
capacity decides who gets investigated. Vendors like
<a href="https://www.niceactimize.com/press-releases/NICE-Actimizes-AIInfused-Transaction-Monitoring-Solution-to-Transform-Chinas-SPD-Silicon-Valley-Bank-AML-Operations-265/">NICE Actimize</a>
keep an expert-written red-flag rule library at the core (the part regulators
recognize and compliance teams can extend the day a new scheme appears), with ML
ranking and de-noising on top. In Winnow, model risk scores are ordinary
columns; the typology rules, thresholds, and routing around them are plain text
a compliance officer can read, tune, and defend to a regulator. More patterns:
<a href="../ai-and-rules-engines/">Deep Dive: AI + Rules Engines</a>.</p>
</div>

## Why Winnow fits

- **All three signal types in one rule:** monetary thresholds, jurisdiction and
  product categories, and watchlist name matching,
  including short or one-off terms added on the fly.
- **Precision through combination:** joining conditions with `AND` / `OR` / `NOT`
  narrows alerts to genuine risk, directly attacking the false-positive problem.
- **Batch and real-time:** screen the day's transactions in one pass, or check a
  single payment inline before it settles.
- **Auditable and adjustable:** rules are explicit text a compliance officer can
  read and revise, applied to a live system
  with no downtime, which matters when regulations change.
