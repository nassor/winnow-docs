+++
title = "Fraud & transaction screening"
description = "Score every transaction against risk rules in real time, catching fraud without blocking good customers."
weight = 10

[extra]
theme = "risk"
+++

Every payment is a yes/no decision made under a deadline: approve it, decline it,
or send it for review, in the moment, before the customer gives up. That is a rule
engine's home turf.

<div class="callout callout-perf">
<p class="callout-title">⚡ Where speed pays for itself</p>
<p>Screening runs inside the payment-authorization deadline. A rules service that is too slow forces a bad trade: time out and approve unscreened (take the fraud loss), or add delay (lose the sale to checkout abandonment). Winnow decides in microseconds, inside your own payment software, so the whole transaction stream is screened without a dedicated server fleet: fraud caught and deadlines kept, on less hardware.</p>
</div>

## The stakes

Card fraud is large and growing: global card-fraud losses reached **$33.41 billion
in 2024** and are projected to climb to about **$41 billion by 2030**
([Nilson Report](https://nilsonreport.com/articles/card-fraud-losses-worldwide-2024/)).

But the subtler cost is **over-blocking**. When screening is too blunt, it rejects
good customers: "false declines." Industry analyses estimate merchants lose *more*
to false declines than to fraud itself (by one estimate roughly **13×** more), and
that about **42% of wrongly declined shoppers abandon the purchase entirely**
([NoFraud](https://www.nofraud.com/blog/the-value-of-false-declines-is-more-than-13-times-the-total-amount-lost-to-actual-card-fraud/),
[Riskified](https://www.riskified.com/learning/ecommerce-checkout-optimization/false-declines/)).
So the goal is **precise** screening, evaluated fast, not just aggressive blocking.

## How it works

Risk analysts express each red flag as a rule and the engine scores every
transaction against all of them in real time. A single rule might say *"flag a
transaction over $5,000 (amount is in cents) from a high-risk country on a
device first seen less than a week ago."* Categories such as country are stored as
numeric codes; free-text fields (a memo, a merchant name) can be matched by
text search. Dozens of such rules combine
with plain and/or/not logic, and thresholds are tuned by editing text; no code change.

<div class="callout callout-ai">
<p class="callout-title">🤖 With AI in the loop</p>
<p>This is how the industry's best fraud stacks already work: a machine-learning
model scores the payment, and <em>rules make the decision</em>. <a href="https://docs.stripe.com/radar/risk-evaluation">Stripe Radar</a>
scores every payment 0–99 and lets merchants write rules combining that score
with amount, payment method, and geography. Its default controls kick in at
scores of 65 (elevated) and 75 (high). <a href="https://docs.adyen.com/risk-management/configure-your-risk-profile/machine-learning-rules">Adyen's Protect</a>
blocks on its ML risk classification only at a merchant-set threshold, with
explicit allow-rules that override the model. In Winnow the model's score is
simply one more column: a risk score of 75 or more on an amount over $5,000 is a
microsecond rule check, and when a fraud wave hits, analysts move the threshold
in minutes instead of waiting weeks for a retrained model. More patterns:
<a href="../ai-and-rules-engines/">Deep Dive: AI + Rules Engines</a>.</p>
</div>

## Why Winnow fits

- **Every kind of check is native.** Amount and velocity thresholds are
  number checks; country, merchant category, and device
  type are category checks; memos and payee
  names are text matches, each stored the way
  that makes its check fastest.
- **Real-time or batch.** Score one transaction in microseconds on the authorization
  path, or sweep the whole day's file in a single pass.
- **Tune live, safely.** Rules are plain text, so an analyst can adjust a threshold
  on a running system with no downtime; decisions already in flight still see a
  complete, consistent rulebook.
- **No extra hop.** Winnow runs as a library *inside* your payment service, so no
  part of the authorization deadline is spent calling out to a separate rules
  server.
