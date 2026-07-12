+++
title = "Ad & content targeting"
description = "Match each impression against millions of targeting rules inside the 100 ms bidding window."
weight = 50

[extra]
theme = "growth"
+++

Online advertising is the textbook "many rules, one event, tiny deadline" problem;
it's the use case Winnow's design fits most directly.

<div class="callout callout-perf">
<p class="callout-title">⚡ Where speed pays for itself</p>
<p>On the bidding path, a decision that misses the ~100 ms window is not a slow bid; it is <em>no</em> bid, and that revenue is gone. Evaluating one impression against millions of rules in microseconds lets you bid on inventory a slower engine would forfeit, at millions of requests per second on a fraction of the hardware. Latency here is revenue: Amazon measured every +100 ms at roughly −1% of sales, so retrieval speed is the margin, not a nice-to-have.</p>
</div>

## The stakes

When a page loads, an auction decides which ad to show, and it has almost no time.
The real-time bidding round trip completes in **under 100 milliseconds**; the
industry [OpenRTB](https://www.playwire.com/the-experts-guide-to-real-time-bidding)
bid-response window is about **100 ms**, and a bid that arrives late is simply
dropped. Exchanges field **millions of bid requests per second**
([AWS](https://www.cloudthat.com/resources/blog/accelerating-programmatic-advertising-with-aws-rtb-fabric)).
Latency is money: Amazon famously found every **100 ms of added latency cost ~1% of
sales**. If your targeting decision is slow, you don't bid, and you don't earn.

The prize is large and growing fast. The real-time-bidding market was about **$28
billion in 2025** and is projected to grow on the order of **33% a year, to ~$276
billion by 2033** ([Market Data Forecast](https://www.marketdataforecast.com/market-reports/real-time-bidding-market)),
carrying well over **half a trillion dollars** of annual programmatic ad spend
([Statista](https://www.statista.com/statistics/275806/programmatic-spending-worldwide/)).

## How it works: the reverse query (a "percolator")

Most queries match *one rule against many records*. Targeting inverts it: a single
**impression** (the event: this user, this app, this moment) is matched against
**millions of campaign rules**, each describing the audience and context a campaign
wants. The engine returns the campaigns whose rules the impression satisfies; those
enter the auction. A campaign's targeting rule might say *"bid when the app is 91,
the country is the US (id 1) or unspecified (0), the user is 18–34, and the OS is
at least version 14."* The incoming impression's attributes
are tested against every campaign's rule at once.

<div class="callout callout-ai">
<p class="callout-title">🤖 With AI in the loop</p>
<p>In every major ad platform, rules run <em>before</em> the AI. <a href="https://eng.snap.com/machine-learning-snap-ad-ranking">Snap</a>
makes trillions of ranking predictions a day, but the first stage of its funnel
is pure rules: targeting, budget, privacy, and policy checks decide which of
millions of ads are even eligible before any model scores a candidate.
<a href="https://www.facebook.com/business/news/good-questions-real-answers-how-does-facebook-use-machine-learning-to-deliver-ads">Meta</a>
admits only ads whose targeting includes the person into the auction; at
<a href="https://www.infoq.com/presentations/pinterest-ads/">Pinterest</a>, the
whole funnel (billions of items down to 1,000–2,000 ranking candidates) must
fit a 200–300 ms budget. That front gate is exactly Winnow's job: prune
millions of campaign rules to a shortlist in microseconds, so the expensive
ranking model only ever sees ads that could actually serve. More patterns:
<a href="../ai-and-rules-engines/">Deep Dive: AI + Rules Engines</a>.</p>
</div>

## Why Winnow fits

- **Measured at this scale.** In [Deep Dive: Cost Savings](@/rule-engines/cost-savings.md),
  Winnow matches an event against **2,000,000 standing rules in ~5.5 microseconds**;
  that's the exact shape described above, with room to spare in the window.
- **The whole rulebook in one pass.** Rules are checked together, not one by one,
  and the engine starts with the condition that eliminates the most,
  so most campaigns are ruled out having barely been looked at.
- **Headroom in the budget.** Microsecond matching leaves essentially the whole
  ~100 ms window for the rest of the bid pipeline, instead of spending it on the match.
- **Runs inside your bidder.** The matcher is a library embedded in your own
  bidding software. No call to a separate rules service sits on the hot path.
