+++
title = "Winnow"
description = "Make millions of automated decisions: faster, and far cheaper."
template = "index.html"
+++

# Winnow

Winnow makes the **instant decisions** behind modern software (*is this payment
fraud, what price do you see, which ad, do you qualify, is this allowed*)
**faster, and far cheaper**.

Almost every instant "yes or no" online runs through a **rule engine**: software
that checks each case against a set of conditions (the *rules*) and returns what
matches. It is the quiet workhorse behind whether a payment looks risky, which
discount you qualify for, what ad you see, whether a loan can be approved instantly,
and who is allowed to open a document. This page, written for a non-technical
reader, explains the idea, why it matters to a business, and the twelve concrete
ways Winnow can be put to work.

## What is a rule engine?

Most business decisions are really a checklist: *if these things are true, then do
that.* A rule engine separates that checklist (the **policy**) from the software
that runs it (the **plumbing**). A business expert writes rules in plain terms:
"flag transactions over $5,000 from a new device," "offer free shipping to loyalty
members spending over $100," and the engine evaluates them quickly, consistently,
and at scale against every record or incoming event.

Think of it as airport security for your data: a fixed set of checks applied to
everyone who passes through, the same way every time, fast enough not to hold up
the line.

<figure class="wv-figure">
<div class="wv-flow">
  <div class="wv-node wv-node--data">
    <span class="wv-eyebrow">An event arrives</span>
    <b>One case to decide</b>
    <span class="wv-ex">a $5,000 payment from a new device</span>
  </div>
  <span class="wv-arrow" aria-hidden="true"></span>
  <div class="wv-node wv-node--rules">
    <span class="wv-eyebrow">The rulebook</span>
    <b>Checked against the policy</b>
    <span class="wv-ex">“flag payments &gt; $5k from a new device” · “free shipping over $100”</span>
  </div>
  <span class="wv-arrow" aria-hidden="true"></span>
  <div class="wv-node wv-node--out">
    <span class="wv-eyebrow">The answer</span>
    <b>What matches</b>
    <span class="wv-ex">flag this one for review</span>
  </div>
</div>
<p class="wv-cap">A rule engine separates the <b>policy</b> (the checklist a business expert writes) from the <b>plumbing</b> (the software that runs it), the same checks applied to every event, fast enough not to hold up the line.</p>
</figure>

## How organizations use them

- **Automate repeatable decisions** that would otherwise be buried in code or done
  by hand: *who qualifies, what price, is this risky, which offer, who can access.*
- **Change the rules without re-engineering.** Analysts edit the rules; there is no
  software release for every policy tweak. This is the difference between reacting
  to a new fraud pattern in **hours instead of weeks**.
- **Apply the same logic everywhere**, consistently and with an auditable trail,
  which regulators increasingly require.
- **Operate in real time and at scale**, across millions of records or events,
  under a deadline measured in milliseconds.

Decision automation is a real and growing market: analysts size the core
business-rules / decision-management software (BRMS) tier at roughly **$1.8 billion
in 2025**, growing about **12% a year** ([MarketsandMarkets](https://www.marketsandmarkets.com/Market-Reports/business-rules-management-system-market-210469074.html)),
with **financial services about a third** of it: credit decisions, fraud, and
compliance ([Mordor Intelligence](https://www.mordorintelligence.com/industry-reports/business-rules-management-systems-market)).
That core sits inside far larger markets it helps run: **fraud detection and
prevention software alone is about $32 billion in 2025, heading for ~$66 billion by
2030** ([MarketsandMarkets](https://www.marketsandmarkets.com/PressReleases/fraud-detection-prevention.asp)).

## Why it matters

The value of a rule engine is **agility, consistency, transparency, and scale**.
The cost of *not* having one shows up as slow policy changes, decisions that differ
from team to team, revenue left on the table, and risk that slips through. The
engines most companies run on haven't really changed since the 1980s: they are slow,
and they burn through expensive computing power. Each use case below pairs that
business case with a number.

## What Winnow does

Instead of reading the rulebook one line at a time, Winnow checks **hundreds of
records in a single step**, like scanning a whole spreadsheet column at once instead
of cell by cell. Same rules, same answers, at a fraction of the time and cost. It is
a **high-performance decisioning core you embed in your own product**, and it
answers two questions in *microseconds*, at large scale:

- **"Which records match these rules?"**: filter millions of rows by any
  combination of conditions.
- **"Which of these millions of rules match this event?"**: the reverse query (or
  *percolator*) at the heart of targeting, screening, and routing.

It does this in two ways. Every attribute is organized in advance
so that a rule is answered by combining a few compressed summaries, checking
hundreds of records in a single machine step instead of reading them one by one.
And every decision reads a complete, frozen snapshot
of the data; updates are prepared off to the side and swapped in whole, so rules
and data can change *live* without pausing or corrupting a single decision. Rules
themselves are short lines of plain text, edited and applied on a
running system without redeploying code.

<figure class="wv-figure">
<p class="wv-title">The Winnow shift: same rulebook, read a smarter way</p>
<div class="wv-split">
  <div class="wv-panel wv-panel--old">
    <h4>Today's engines</h4>
    <p class="wv-sub">read the rulebook one line at a time, over and over, billions of times a day</p>
    <svg class="wv-svg" viewBox="0 0 200 120" role="img" aria-label="A grid of cells read one at a time along a slow zig-zag path">
      <defs><marker id="ah-old" markerWidth="7" markerHeight="7" refX="5" refY="3" orient="auto"><path d="M0,0 L6,3 L0,6 Z" fill="#64748b"/></marker></defs>
      <g fill="#f8fafc" stroke="#cbd5e1"><rect x="14" y="14" width="38" height="26" rx="3"/><rect x="58" y="14" width="38" height="26" rx="3"/><rect x="102" y="14" width="38" height="26" rx="3"/><rect x="146" y="14" width="38" height="26" rx="3"/><rect x="14" y="46" width="38" height="26" rx="3"/><rect x="58" y="46" width="38" height="26" rx="3"/><rect x="102" y="46" width="38" height="26" rx="3"/><rect x="146" y="46" width="38" height="26" rx="3"/><rect x="14" y="78" width="38" height="26" rx="3"/><rect x="58" y="78" width="38" height="26" rx="3"/><rect x="102" y="78" width="38" height="26" rx="3"/><rect x="146" y="78" width="38" height="26" rx="3"/></g>
      <polyline points="33,27 165,27 165,59 33,59 33,91 165,91" fill="none" stroke="#64748b" stroke-width="2.5" stroke-linejoin="round" stroke-linecap="round" marker-end="url(#ah-old)"/>
    </svg>
  </div>
  <div class="wv-panel wv-panel--new">
    <h4>Winnow</h4>
    <p class="wv-sub">scans a whole column in a single step, like reading a spreadsheet column at once</p>
    <svg class="wv-svg" viewBox="0 0 200 120" role="img" aria-label="The same grid, with one whole column read in a single downward sweep">
      <defs><marker id="ah-new" markerWidth="8" markerHeight="8" refX="5" refY="3" orient="auto"><path d="M0,0 L6,3 L0,6 Z" fill="#157f3c"/></marker></defs>
      <g fill="#f8fafc" stroke="#cbd5e1"><rect x="14" y="14" width="38" height="26" rx="3"/><rect x="102" y="14" width="38" height="26" rx="3"/><rect x="146" y="14" width="38" height="26" rx="3"/><rect x="14" y="46" width="38" height="26" rx="3"/><rect x="102" y="46" width="38" height="26" rx="3"/><rect x="146" y="46" width="38" height="26" rx="3"/><rect x="14" y="78" width="38" height="26" rx="3"/><rect x="102" y="78" width="38" height="26" rx="3"/><rect x="146" y="78" width="38" height="26" rx="3"/></g>
      <g fill="#dcfce7" stroke="#157f3c"><rect x="58" y="14" width="38" height="26" rx="3"/><rect x="58" y="46" width="38" height="26" rx="3"/><rect x="58" y="78" width="38" height="26" rx="3"/></g>
      <line x1="77" y1="6" x2="77" y2="112" stroke="#157f3c" stroke-width="2.5" stroke-linecap="round" marker-end="url(#ah-new)"/>
    </svg>
  </div>
</div>
<p class="wv-cap">Same rules, same answers: <span class="wv-inw">a fraction of the time and cost</span>. Winnow answers from pre-built column summaries, so it never reads the records one by one.</p>
</figure>

The payoff is decisions in **microseconds**, a **much smaller cloud bill**, and rules
your team can change **live**, without waiting for a software release. Winnow is the
fast **matching and decisioning** layer. It answers *which rules or records apply,
right now*. It does not chain conclusions into further conclusions the way classic
"expert system" engines do.

For the evidence behind "faster and far cheaper," see
[**Deep Dive: Cost Savings**](@/rule-engines/cost-savings.md), where Winnow measured
head-to-head against a hand-built program, a traditional database, and an analytics
database on the same real-time rule, with the annual cloud bill each implies.

And if your roadmap says "AI," start with
[**Deep Dive: AI + Rules Engines**](@/rule-engines/ai-and-rules-engines.md), with six patterns,
drawn from named production systems at Stripe, HSBC, Meta, Amazon, Visa, and
others, showing how machine-learning models and a fast rules layer divide the
work, and why AI *multiplies* the number of rule decisions rather than
replacing them.

## Twelve ways to put it to work

The use cases below fall into three themes: <span class="theme-label theme-risk">Risk &amp; compliance</span>, <span class="theme-label theme-growth">Growth &amp; personalization</span>, and <span class="theme-label theme-ops">Operations &amp; data</span> (the same grouping used in the
sidebar). Each pairs the business case, with a cited figure, against the specific
Winnow capabilities it relies on.
