+++
title = "Deep Dive: Cost Savings"
description = "Winnow measured against a hand-built program, a traditional database, and an analytics database on the same real-time rule, with the cloud bill each implies."
weight = 1
+++

A rule engine's job is to take one event and check it against a book of rules:
*does this transaction match any fraud pattern? which offers does this customer
qualify for? is this request allowed?* The honest question for a buyer is simple:
**how fast, and at what cost, compared with the alternatives you'd actually reach
for?**

So we built the comparison: the same rule, the same data, the same machine,
testing Winnow against a hand-written program, a traditional database (SQLite), and a
modern analytics database (DuckDB). Before anything is timed, the benchmark
verifies that every engine returns the identical answers. The code ships in the
repository.

<div class="callout callout-perf">
<p class="callout-title">⚡ The result</p>
<p>On a realistic real-time rule, <strong>Winnow answers each decision in about
2.4 microseconds, hundreds to tens of thousands of times faster</strong> than the
alternatives. To sustain 100,000 decisions a second, around the clock, Winnow
needs <strong>about a quarter of one processor core (~$90/year)</strong>; the same
workload takes roughly <strong>330 cores (~$124K/year)</strong> on the analytics
database or <strong>5,400 cores (~$2.0M/year)</strong> on the traditional
database. And Winnow holds that speed <strong>while the data is being updated
live</strong>, which is where the alternatives stall.</p>
</div>

*(Units: **µs** is a microsecond, a millionth of a second; **ms** is a
millisecond, a thousandth. A payment authorization budget is typically tens of
milliseconds; a web page render, a few hundred.)*

## The test

Each "decision" checks **one rule with ten conditions spanning ten different
columns** (the kind of multi-condition rule real fraud, eligibility, and
marketing policies use) against a table of **3 million records**. Every engine
computes the *identical* answer (the benchmark cross-checks this before timing
anything).

The databases run **without an index**, on purpose. That is the realistic
setting, not a handicap:

- A rule book holds thousands of different rules, each looking at a *different*
  combination of columns. To make a database fast for all of them you would need
  an index for every possible combination; there are far too many.
- And because the data changes constantly, every extra index is a tax on every
  update. The more you index for reading, the more you pay on writing.

So in practice a database evaluating arbitrary rules does the only thing it can:
it **scans**: reads through the data row by row. Winnow never scans. Every
column is already organized as a compressed index,
so a rule is answered by combining a few pre-built summaries: set arithmetic,
not row-by-row reading.

## How much faster

One decision at a time, on a single processor core:

<figure class="wv-figure">
<p class="wv-title">Time per decision<span class="wv-dir">← shorter is faster · log scale</span></p>
<div class="wv-bars" style="--decades:5">
  <div class="wv-bar is-winnow" style="--w:7.6%">
    <span class="wv-bar-name">Winnow</span>
    <span class="wv-bar-track"><span class="wv-bar-fill"></span></span>
    <span class="wv-bar-val">~2.4&nbsp;µs<small>fastest</small></span>
  </div>
  <div class="wv-bar" style="--w:57.5%">
    <span class="wv-bar-name">Hand-written<small>one core</small></span>
    <span class="wv-bar-track"><span class="wv-bar-fill"></span></span>
    <span class="wv-bar-val">~750&nbsp;µs<small>≈310× slower</small></span>
  </div>
  <div class="wv-bar" style="--w:70.4%">
    <span class="wv-bar-name">Analytics DB<small>DuckDB · no index</small></span>
    <span class="wv-bar-track"><span class="wv-bar-fill"></span></span>
    <span class="wv-bar-val">3.3&nbsp;ms<small>≈1,400× slower</small></span>
  </div>
  <div class="wv-bar" style="--w:94.6%">
    <span class="wv-bar-name">Traditional DB<small>SQLite · no index</small></span>
    <span class="wv-bar-track"><span class="wv-bar-fill"></span></span>
    <span class="wv-bar-val">~54&nbsp;ms<small>≈22,000× slower</small></span>
  </div>
  <div class="wv-axis"><span></span><span class="wv-ticks"><span>1µs</span><span>10µs</span><span>100µs</span><span>1ms</span><span>10ms</span><span>100ms</span></span><span></span></div>
</div>
<p class="wv-cap">Each gridline is <b>10× slower</b> than the one before it. The databases must read millions of rows to answer one decision; <span class="wv-inw">Winnow reads none</span>. It combines pre-built column summaries.</p>
</figure>

The gap is structural, not a tuning trick. The analytics database scans far
faster than the traditional one, but still carries **a few milliseconds of fixed
overhead on every query**: fine for a big hourly report, fatal for millions of
small decisions a second.

## Many decisions at once, even while the data changes

Real systems do not make one decision at a time, and their data never sits
still. So we drove the engine from every core for a fixed window, first on
static data, then **with a writer changing the data in flight**, and measured
throughput and the slow tail (how long the slowest 1-in-100 decisions take):

<figure class="wv-figure">
<p class="wv-title">Decisions per second, one machine<span class="wv-dir">longer is more · log scale</span></p>
<div class="wv-bars" style="--decades:4">
  <div class="wv-bar is-winnow" style="--w:77.0%">
    <span class="wv-bar-name">Winnow</span>
    <span class="wv-bar-track"><span class="wv-bar-fill"></span></span>
    <span class="wv-bar-val">~12M<small>static &amp; live: unchanged</small></span>
  </div>
  <div class="wv-bar" style="--w:6.8%">
    <span class="wv-bar-name">Hand-built<small>scan behind a lock</small></span>
    <span class="wv-bar-track"><span class="wv-bar-fill"></span></span>
    <span class="wv-bar-val">~18,700<small>≈640× fewer</small></span>
  </div>
  <div class="wv-axis"><span></span><span class="wv-ticks"><span>10K</span><span>100K</span><span>1M</span><span>10M</span><span>100M</span></span><span></span></div>
</div>
<p class="wv-cap"><b>Winnow holds ~12M decisions/second whether the data is static or updated live</b>. No locks on the read path. <span class="wv-inw">The lock-based store makes readers queue behind the writer</span>, so its throughput dips and its slow tail grows by about a third (2.4&nbsp;ms&nbsp;→&nbsp;3.2&nbsp;ms) the moment updates start.</p>
</figure>

That flat line is the whole point. Every decision reads a frozen, consistent
snapshot of the data, while each update is prepared off to the side and swapped
in whole, so decisions never wait for updates: throughput holds and Winnow's own
slow tail stays put at **10 µs**, static or live. Anything that instead guards
its data with a lock (a hand-rolled store, or a database under write load)
answers each decision in ~1.6 ms and stalls its readers the moment a writer
arrives. **Rules and data can change live, without pausing a single decision.**

## What it costs

Speed is really a cost story: a faster decision is a smaller server bill for the
same work. Cloud computing is rented by the processor core, so we sized each
engine to sustain **100,000 decisions per second, around the clock**, at a
typical cloud rate (~$0.043 per core-hour):

<figure class="wv-figure">
<p class="wv-title">Annual cloud bill · 100,000 decisions/second, around the clock<span class="wv-dir">← cheaper is better · log scale</span></p>
<div class="wv-bars" style="--decades:6">
  <div class="wv-bar is-winnow" style="--w:15.9%">
    <span class="wv-bar-name">Winnow<small>¼ of one core</small></span>
    <span class="wv-bar-track"><span class="wv-bar-fill"></span></span>
    <span class="wv-bar-val">~$90<small>cheapest</small></span>
  </div>
  <div class="wv-bar" style="--w:57.5%">
    <span class="wv-bar-name">Hand-written<small>~75 cores</small></span>
    <span class="wv-bar-track"><span class="wv-bar-fill"></span></span>
    <span class="wv-bar-val">~$28K<small>/year</small></span>
  </div>
  <div class="wv-bar" style="--w:68.2%">
    <span class="wv-bar-name">Analytics DB<small>DuckDB · ~330 cores</small></span>
    <span class="wv-bar-track"><span class="wv-bar-fill"></span></span>
    <span class="wv-bar-val">~$124K<small>/year</small></span>
  </div>
  <div class="wv-bar" style="--w:88.4%">
    <span class="wv-bar-name">Traditional DB<small>SQLite · ~5,400 cores</small></span>
    <span class="wv-bar-track"><span class="wv-bar-fill"></span></span>
    <span class="wv-bar-val">~$2.0M<small>/year</small></span>
  </div>
  <div class="wv-axis"><span></span><span class="wv-ticks"><span>$10</span><span>$100</span><span>$1K</span><span>$10K</span><span>$100K</span><span>$1M</span><span>$10M</span></span><span></span></div>
</div>
<p class="wv-cap">The same 100,000 decisions every second, all year. <span class="wv-inw">Winnow's bill is a rounding error</span>; the database path is a permanent six- to seven-figure line item.</p>
</figure>

These scale with volume: a large platform making **a million** decisions a
second multiplies every figure by ten: Winnow at roughly $900 a year, the
traditional-database path around **$20 million**. The faster engine doesn't just
lower latency; it removes almost the entire line item.

## Why Winnow wins this workload

- **No scanning.** A rule combines a few pre-built column summaries instead of
  reading rows; a database without the exact right index has no choice but to.
- **No per-query overhead.** A rule compiles once, then just runs: no SQL to
  parse and plan per decision. That fixed cost is what caps the analytics
  database near a few hundred decisions a second per core while Winnow clears
  four hundred thousand.
- **Updates never block decisions.** Reads take a frozen snapshot; updates are
  published beside it in one step: no locks on the read path, so speed holds
  while the data changes.

## When the columns *are* known

The case above is deliberately the hard one: arbitrary rules, no usable index.
But sometimes you *do* know which columns matter, and a database would shine
there: build an index on the key column, and each query jumps straight to a
handful of rows. Winnow covers that case too, two ways:

- **A selective key + filters.** When one condition pins the search to a handful
  of records (a specific merchant, account, or app), Winnow uses that condition's
  index to find the few survivors and checks the remaining conditions against
  only those, the same "seek, then filter" a database index performs. Measured
  on exactly that shape (2 million rows): **Winnow answers in ~1.2 µs, about
  11× faster than the traditional database *with* its index (~13 µs)**, and
  roughly 370× and 520× faster than the hand-written scan and the indexed
  analytics database. (An earlier version of Winnow lost this comparison about
  50 to 1; the current engine wins it outright. You give up nothing by adopting
  Winnow even on a database's best workload.)
- **A combination that's only selective together.** When no single column
  narrows the search but a pair does (two common attributes that are rare in
  combination), you can declare a **composite index** on the pair; rules that
  match it then resolve in one small lookup instead of combining two large sets,
  measured at **roughly 100× faster** on those rules (1–2 million rows), with
  no change to how the rules are written.

## The other direction: a book of standing rules

Everything above asks "which records match *this rule*?". The mirror question,
**"this event just arrived; which of my thousands of standing rules does it
trigger?"**, is the shape behind fraud-rule books, ad targeting, and alert
routing. Winnow can index this two ways (a database can only do the second):

- a **rule index**: each rule is filed under its most distinctive condition, so
  an incoming event checks only the handful of rules it could possibly trigger;
  and
- a **column index**: the rules themselves are stored as a table and their
  conditions indexed, exactly what a database does, but answered with Winnow's
  index arithmetic instead of SQL.

We registered **200,000 rules** (each of the form *"this merchant, amounts in
this range, score at least this"*) and asked each engine *which rules fire* for
each incoming event (every engine returns the identical set, cross-checked before timing):

<figure class="wv-figure">
<p class="wv-title">Which of 200,000 standing rules fire: time per event<span class="wv-dir">← shorter is faster · log scale</span></p>
<div class="wv-bars" style="--decades:5">
  <div class="wv-bar is-winnow" style="--w:10.9%">
    <span class="wv-bar-name">Winnow<small>column index</small></span>
    <span class="wv-bar-track"><span class="wv-bar-fill"></span></span>
    <span class="wv-bar-val">~0.35&nbsp;µs<small>fastest</small></span>
  </div>
  <div class="wv-bar is-winnow" style="--w:19.6%">
    <span class="wv-bar-name">Winnow<small>rule index</small></span>
    <span class="wv-bar-track"><span class="wv-bar-fill"></span></span>
    <span class="wv-bar-val">~0.95&nbsp;µs<small>arbitrary rules</small></span>
  </div>
  <div class="wv-bar" style="--w:36.9%">
    <span class="wv-bar-name">Traditional DB<small>SQLite · indexed key</small></span>
    <span class="wv-bar-track"><span class="wv-bar-fill"></span></span>
    <span class="wv-bar-val">~7&nbsp;µs<small>≈20× slower</small></span>
  </div>
  <div class="wv-bar" style="--w:52.0%">
    <span class="wv-bar-name">Hand-written<small>one core</small></span>
    <span class="wv-bar-track"><span class="wv-bar-fill"></span></span>
    <span class="wv-bar-val">~40&nbsp;µs<small>≈115× slower</small></span>
  </div>
  <div class="wv-bar" style="--w:72.0%">
    <span class="wv-bar-name">Analytics DB<small>DuckDB · indexed key</small></span>
    <span class="wv-bar-track"><span class="wv-bar-fill"></span></span>
    <span class="wv-bar-val">~400&nbsp;µs<small>≈1,140× slower</small></span>
  </div>
  <div class="wv-bar" style="--w:90.9%">
    <span class="wv-bar-name">Traditional DB<small>SQLite · no index</small></span>
    <span class="wv-bar-track"><span class="wv-bar-fill"></span></span>
    <span class="wv-bar-val">~3.5&nbsp;ms<small>≈10,000× slower</small></span>
  </div>
  <div class="wv-axis"><span></span><span class="wv-ticks"><span>0.1µs</span><span>1µs</span><span>10µs</span><span>100µs</span><span>1ms</span><span>10ms</span></span><span></span></div>
</div>
<p class="wv-cap"><b>Even against a database with an index on the key column (its best case), both Winnow paths win.</b> <span class="wv-inw">The column index is ~20× faster</span> than the indexed database; the rule index handles arbitrary, mixed rules and stays fast into the millions.</p>
</figure>

- **Both Winnow paths beat the indexed database.** Even with its key index, the
  traditional database runs the same "find candidates, then verify" strategy,
  but pays to parse and plan SQL on every event. Winnow skips that: the key's
  index prunes 200,000 rules to ~20 candidates, direct index arithmetic finishes
  them, and the hot path reuses its working memory between events to keep
  per-event time flat. The analytics database, *with* its key index, carries the
  per-query overhead of a batch-report engine, not a point-decision one. (To
  sustain 100,000 events per second: Winnow needs well under half of one core;
  the indexed analytics database, about 40.)
- **Two indexes for two situations.** The column index is fastest when the rules
  share one shape (as here). The rule index handles **arbitrary, mixed rules**
  (each keying on different columns) and stays fast as the book grows:
  registering and indexing all 200,000 rules takes ~170 ms, and growing the book
  barely moves per-event latency: at **2 million rules an event still matches
  in ~1.6 µs**, and rebuilding the whole index takes about two seconds.

## Honest conditions

- **Measured on one 16-core (32-thread) desktop workstation** (AMD Ryzen 9
  9950X3D-class, 128 GB RAM), July 2026, over 3 million records; every engine
  runs in memory, and all numbers come from the same benchmark run. The
  advantage *grows* with data: re-measured at **10 million rows**, the
  traditional database takes ~196 ms per decision (it reads every row, so its
  time grows with the data) while Winnow takes ~6.6 µs; the gap widens to
  ~30,000×. The analytics database is the exception: its time barely grows
  (3.3 ms → 4.5 ms), because it is dominated by a *fixed* per-query overhead.
  That overhead is exactly what rules it out for high-decision-rate work, at any
  data size.
- **The test rules are specific** (each matches almost no records), which is the
  realistic hot path: most rules don't fire for most events, and fast rejection
  is what a screening engine does all day. For broad rules that match a large
  fraction of the data, an analytics database's scan narrows the gap; that
  batch-analytics shape is its home turf, not Winnow's target.
- **The databases are excellent tools** used as intended (reporting, ad-hoc
  analytics). This comparison is about a *different* job: millions of small,
  arbitrary, many-column rule decisions a second on live data, which is what
  Winnow is built for.
