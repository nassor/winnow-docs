+++
title = "Dynamic pricing & offers"
description = "Decide which price, discount, or offer applies to each customer and basket, instantly."
weight = 60

[extra]
theme = "growth"
+++

Which promotion applies to this cart? Does this customer get the loyalty price?
Is this bundle eligible for free shipping? Pricing and offers are a stack of
eligibility rules, evaluated fresh on every page view.

<div class="callout callout-perf">
<p class="callout-title">⚡ Where speed pays for itself</p>
<p>Pricing is computed on the page-load critical path, and page latency <em>is</em> conversion. Amazon measured every +100 ms at about −1% of sales. Microsecond evaluation keeps pricing and promotion rules off the render budget even at peak traffic, so you capture the upside of dynamic pricing without paying for it in lost conversions or a pricing-service fleet sized for Black Friday.</p>
</div>

## The stakes

Getting this right moves real money. By McKinsey's conservative benchmarks, dynamic
pricing delivers roughly **2–5% sales growth and 5–10% margin improvement**
([McKinsey](https://www.mckinsey.com/capabilities/growth-marketing-and-sales/how-we-help-clients/dynamic-pricing));
some studies report margin gains as high as ~25% in travel and e-commerce. More
broadly, personalized offers lift revenue by **5–15%**, and **71% of consumers now
expect** personalized interactions while **76% are frustrated** when they don't get
them ([McKinsey](https://www.mckinsey.com/capabilities/growth-marketing-and-sales/our-insights/the-value-of-getting-personalization-right-or-wrong-is-multiplying)).

## How it works

Pricing and promotion eligibility is encoded as rules over the basket and the
customer; the engine returns which offers apply, and the app picks the best one.
For example, *"members in tier 2 or 3, shopping in region 5, spending $100 or more
on category 88, qualify for this promotion."* Because rules are plain text, a merchandiser can
launch a flash sale or a regional campaign by adding rules, not by waiting on a
software release.

<div class="callout callout-ai">
<p class="callout-title">🤖 With AI in the loop</p>
<p>AI pricing only ships inside rule guardrails. <a href="https://www.kdd.org/kdd2018/accepted-papers/view/customized-regression-model-for-airbnb-dynamic-pricing">Airbnb's Smart Pricing</a>
models suggest a nightly price for each of 4 million+ listings, but a suggestion
is adopted only if it falls inside the host's own minimum/maximum; the host's
rule, not the model, has the final say on every published price. Airline pricing
at <a href="https://pros.com/learn/blog/key-strategies-implementing-airline-dynamic-pricing-part-one/">PROS</a>
(400 million+ prices a day) applies rule-based adjustments and guardrails around
its forecasting models; insurer <a href="https://earnix.com/assets/case-study-warta-1.pdf">Warta</a>
runs ML price models inside a governed rating-rule process that gets updated
prices to production <strong>within hours</strong>. In Winnow those floors,
ceilings, and eligibility bounds are ordinary rules checked against every model
output, on every page view, and changed as fast as the market moves. More
patterns: <a href="../ai-and-rules-engines/">Deep Dive: AI + Rules Engines</a>.</p>
</div>

## Why Winnow fits

- **Basket and customer attributes map directly:** cart value and quantity are
  number checks; loyalty tier, region, and category are
  category checks; "any of", "between", and
  and/or/not combinations are all native.
- **The question runs in reverse at checkout:** given one basket, *which of
  thousands of promotion rules apply?* (Answered in microseconds at page render.)
- **Promotions change constantly.** Rules are plain text and
  update live, so pricing teams launch and
  retire offers on a running store with no downtime.
- **On every page, for every shopper,** the engine evaluates the whole promotion
  book in one pass, so the cost per page view stays negligible even at peak traffic.
