+++
title = "Customer segmentation & lead scoring"
description = "Define segments as rules and sort your whole base, or place one customer into every segment they match."
weight = 70

[extra]
theme = "growth"
+++

A segment is just a rule: *"high-value, active customers in these regions."* Lead
scoring is the same idea with a threshold. A rule engine lets marketing and growth
teams define those rules and apply them to millions of people at once, or to one
person, in real time.

## The stakes

Segmentation exists to power personalization, and personalization pays. McKinsey
finds it lifts revenue by **5–15%** and marketing ROI by **10–30%**, and that the
**fastest-growing companies earn 40% more of their revenue from personalization**
than slower peers. It is also now expected: **71% of consumers expect personalized
interactions and 76% are frustrated** without them
([McKinsey](https://www.mckinsey.com/capabilities/growth-marketing-and-sales/our-insights/the-value-of-getting-personalization-right-or-wrong-is-multiplying)).

## How it works

A segment definition is a rule over customer attributes: for example,
*"high-value (≥ $500 spent), active (4+ orders), recent (within 90 days) customers
in three regions."* Run it across the whole base to **build the segment**; or run it in
reverse (given one customer, **which of hundreds of segments do they fall into right
now?**) to drive next-best-action at page load.

<div class="callout callout-ai">
<p class="callout-title">🤖 With AI in the loop</p>
<p>Predictive marketing runs model scores through marketer-owned rules.
<a href="https://www.braze.com/docs/user_guide/brazeai/predictive_suite/predictive_churn/messaging_users">Braze</a>
assigns every customer a churn-risk score (0–100) from a model rebuilt about
every two weeks, but marketers act on it through segment filters they can
change today. <a href="https://help.salesforce.com/s/articleView?id=ai.einstein_sales_els_how_it_works.htm&amp;language=en_US&amp;type=5">Salesforce Einstein</a>
retrains its lead-scoring models every ten days and refreshes scores every few
hours, while routing rules on the score assign leads to reps in seconds.
<a href="https://blog.developer.adobe.com/en/publish/2025/06/leveraging-propensity-models-marketing-that-thinks-like-your-customer">Adobe's Customer AI</a>
writes propensity scores onto each profile precisely so segment rules can
threshold them. In Winnow a propensity score is just another numeric column
(customers with a churn score of 80 or more who ordered within the last 30 days,
say), evaluated across the whole base in one pass, with thresholds the team tunes live. More
patterns: <a href="../ai-and-rules-engines/">Deep Dive: AI + Rules Engines</a>.</p>
</div>

## Why Winnow fits

- **Recency, frequency, monetary** values are number checks;
  region, tier, and channel are category checks;
  interests and tags are text matches.
- **The whole base in one pass:** bucket millions of customers at once, and
  re-segment on demand as definitions change.
- **Real-time classification:** place a single customer against every segment in
  microseconds, fast enough for on-page personalization.
- **Marketer-owned rules:** segments are plain text, edited without engineering and
  applied live, with no release cycle.
