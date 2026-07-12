+++
title = "Eligibility, underwriting & benefits"
description = "Turn qualification criteria into rules that decide instantly, consistently, and auditably."
weight = 90

[extra]
theme = "ops"
+++

"Does this applicant qualify?" is a checklist of criteria, whether for a loan,
a policy, a benefit, or a plan. Encoded as rules, that checklist runs in an
instant and gives every applicant the same answer for the same facts.

## The stakes

Automating qualification decisions transforms both speed and cost. Properly designed
automated underwriting lets insurers process **up to ~90%** of applications
straight-through (and lenders even more), turning decisions that took **days or
weeks into minutes or seconds**, with reported **~70% reductions in processing time
and ~40% in cost** ([ScienceSoft](https://www.scnsoft.com/insurance/underwriting-automation)).
Beyond speed, rule-based decisions are **consistent and auditable**: the same
policy for everyone, written down explicitly. That matters when a regulator asks
*why* an application was declined.

## How it works

Qualification criteria become rules, with exclusions stated explicitly. For
example, *"applicants aged 18–75, earning at least $30,000, in credit bands 1–3,
and not in the excluded region 9, qualify."* Winnow can also check applications as they
arrive: required fields present, categories
allowed, numbers within permitted ranges, so malformed data is caught before it
ever reaches a decision.

<div class="callout callout-ai">
<p class="callout-title">🤖 With AI in the loop</p>
<p>AI underwriting is a model wrapped in rules. <a href="https://www.sec.gov/Archives/edgar/data/1647639/000164763925000018/upst-20241231.htm">Upstart</a>,
which fully automated <strong>91% of its loans in 2024</strong>, applies each
bank partner's own underwriting policy (minimum credit scores, loan-amount
limits, maximum debt-to-income) as hard knockout rules <em>before</em> an
application reaches its models. <a href="https://www.zest.ai/product/underwriting">Zest AI</a>
auto-decisions about 80% of applications by putting lender credit policies and
cut-offs around its model scores. That is the same layer that produces the
adverse-action reasons U.S. lending law requires for every automated decline.
In Winnow the model's score is one column among many; the knockouts, caps, and
exclusions around it are explicit text, auditable line by line, and adjustable
the day policy changes. More patterns:
<a href="../ai-and-rules-engines/">Deep Dive: AI + Rules Engines</a>.</p>
</div>

## Why Winnow fits

- **Criteria map directly:** age, income, and tenure are
  number checks; credit band and product are
  category checks; exclusions are `NOT`;
  everything combines with `AND` / `OR`.
- **Relative checks too:** a rule can compare one field against another
  (`requested_amount <= approved_limit`) directly.
- **Consistent and explicit:** every applicant is judged by the same written rules,
  so decisions are reproducible and explainable.
- **Validated input, instant output:** bad data is rejected on arrival; the decision
  itself takes microseconds and stays consistent even while the criteria are being
  updated.
