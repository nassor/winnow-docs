+++
title = "Deep Dive: AI + Rules Engines"
description = "Real AI systems pair a learning model with a rules layer that holds the policy. The six patterns, with named production systems, and where Winnow fits in each."
weight = 2
+++

The most common question about AI and rule engines is which one will replace the
other. The answer, from every production AI system we could examine, is:
**neither. Models estimate; rules decide.**

<figure class="wv-figure">
<div class="wv-flow">
  <div class="wv-node wv-node--model">
    <span class="wv-eyebrow">Model</span>
    <b>Estimates the odds</b>
    <span class="wv-ex">“87% likely fraud” is a probability, learned from data</span>
  </div>
  <span class="wv-arrow" aria-hidden="true"></span>
  <div class="wv-node wv-node--rules">
    <span class="wv-eyebrow">Rules</span>
    <b>Decide the policy</b>
    <span class="wv-ex">“block ≥ 80, unless trusted; &gt; $5k → review” is the part you control</span>
  </div>
  <span class="wv-arrow" aria-hidden="true"></span>
  <div class="wv-node wv-node--out">
    <span class="wv-eyebrow">Result</span>
    <b>One auditable decision</b>
    <span class="wv-ex">exact, explainable, changed in minutes</span>
  </div>
</div>
<p class="wv-cap"><b>Neither replaces the other; every production system ships both.</b> <span class="wv-inw">And AI multiplies the number of rule decisions</span> rather than replacing them: every model score becomes one more input the rules must act on.</p>
</figure>

A *model* is the part that learns from data. It can say "this payment looks 87%
likely to be fraud" (a judgment no one had to write down). A *rule* is the part
your business controls: *"block anything scored above 80, unless it's a
long-standing customer; above $5,000, send it to review."* One supplies the
judgment, the other supplies the policy, and real systems always ship both,
because each is strong exactly where the other is weak:

- **Models find patterns nobody wrote down**, but their answers are
  probabilities, retraining takes days or weeks, and explaining any single
  answer is hard.
- **Rules are exact, auditable, and change in minutes**, but they only know
  what someone thought to write.

What follows are the six ways production systems combine them, each with named,
cited examples, and what each pattern asks of the rules engine. The short
version: **AI doesn't shrink the rules workload; it multiplies it**, because
every model score becomes one more input rules must act on, instantly, on every
event.

## 1. The model scores; the rules decide

The most widespread pattern: the model's output lands next to the business data,
and human-written rules make the actual decision.

<figure class="wv-figure">
<div class="wv-flow">
  <div class="wv-node wv-node--model">
    <span class="wv-eyebrow">Model</span>
    <b>Scores the event</b>
    <span class="wv-ex">“87% likely fraud” is a 0–99 risk score</span>
  </div>
  <span class="wv-arrow" aria-hidden="true"></span>
  <div class="wv-node wv-node--rules">
    <span class="wv-eyebrow">Rules</span>
    <b>Apply the policy</b>
    <span class="wv-ex">block ≥ 80 · unless a long-standing customer · &gt; $5,000 → review</span>
  </div>
  <span class="wv-arrow" aria-hidden="true"></span>
  <div class="wv-node wv-node--out">
    <span class="wv-eyebrow">Decision</span>
    <b>Block · Allow · Review</b>
  </div>
</div>
<p class="wv-cap"><b>Pattern 1: the model scores, the rules decide.</b> <span class="wv-inw">In Winnow: the score is just one more column, and the threshold is a text edit, evaluated in microseconds.</span></p>
</figure>

- **Stripe Radar** scores every payment **0–99** for fraud risk; merchants then
  write rules that combine that score with ordinary business facts (amount,
  payment method, country) to block, allow, or send to review. Stripe's default
  controls treat **65+ as elevated risk and 75+ as high risk**, and merchants
  retune those thresholds from a dashboard, instantly
  ([Stripe docs](https://docs.stripe.com/radar/risk-evaluation)).
- **Sift** returns a real-time fraud score and lets fraud managers act on it
  with criteria like *orders over $500 from Canada with a fraud score above 80*,
  a rules layer designed to add **under 50 ms** even at the 99th percentile,
  because it runs inside the transaction
  ([Sift engineering](https://engineering.sift.com/how-workflows-work/)).
- **Braze** predicts each customer's churn risk (0–100) with a model rebuilt
  about **every two weeks**, but marketers act on it through segment rules they
  can change *today*
  ([Braze docs](https://www.braze.com/docs/user_guide/brazeai/predictive_suite/predictive_churn/messaging_users)).

**In Winnow:** a model score is just another numeric column. A risk score of 75
or higher, on an amount over $5,000, from one of two flagged countries is one rule
evaluation, answered in microseconds alongside every other condition, and the
threshold is a text edit, not a deployment.

## 2. The rules guard the model

Here the model proposes and the rules have veto power, encoding the law, the
brand, or the board's risk appetite as hard bounds the model cannot cross.

<figure class="wv-figure">
<div class="wv-flow">
  <div class="wv-node wv-node--model">
    <span class="wv-eyebrow">Model</span>
    <b>Proposes</b>
    <span class="wv-ex">a nightly price · approve this loan</span>
  </div>
  <span class="wv-arrow" aria-hidden="true"></span>
  <div class="wv-node wv-node--stop">
    <span class="wv-eyebrow">Rules · veto power</span>
    <b>Hard bounds it can't cross</b>
    <span class="wv-ex">min credit score · loan cap · host's min–max price · jurisdiction</span>
  </div>
  <span class="wv-arrow" aria-hidden="true"></span>
  <div class="wv-node wv-node--out">
    <span class="wv-eyebrow">Output</span>
    <b>Only what's inside policy ships</b>
    <span class="wv-ex">otherwise blocked or clamped</span>
  </div>
</div>
<p class="wv-cap"><b>The host's rule, not the model, has the final say on every published price.</b> <span class="wv-inw">In Winnow: floors, ceilings, exclusions, and jurisdiction rules are ordinary conditions</span> on every model output, changed the moment policy changes, nothing retrained.</p>
</figure>

- **Upstart**, whose lending models fully automated **91% of loans in 2024**,
  applies each bank partner's own underwriting policy (minimum credit scores,
  loan-amount limits, maximum debt-to-income) as hard knockout criteria
  *before* an application ever reaches the models
  ([Upstart 10-K filing](https://www.sec.gov/Archives/edgar/data/1647639/000164763925000018/upst-20241231.htm)).
- **Airbnb's Smart Pricing** models suggest a nightly price for each of 4
  million+ listings, but a suggestion is adopted only if it falls inside the
  host's own minimum/maximum. The host's simple rule, not the model, has the
  final say on every published price
  ([KDD 2018 paper](https://www.kdd.org/kdd2018/accepted-papers/view/customized-regression-model-for-airbnb-dynamic-pricing)).
- Airline pricing works the same way at scale: **PROS**, whose platform drives
  **400 million+ prices a day** for carriers, applies rule-based adjustments and
  guardrails around its demand-forecasting models' outputs
  ([PROS](https://pros.com/learn/blog/key-strategies-implementing-airline-dynamic-pricing-part-one/)).

**In Winnow:** floors, ceilings, exclusions, and jurisdiction rules are ordinary
conditions evaluated against every model output, changed the moment policy
changes, with nothing retrained and an exact record of what rule decided what.

## 3. The rules prune; the model ranks

Model inference is expensive; rule evaluation is cheap. So high-volume systems
put a rules gate *in front of* the model and let it discard almost everything.

<figure class="wv-figure">
<div class="wv-flow">
  <div class="wv-node wv-node--data">
    <span class="wv-eyebrow">Candidates</span>
    <b>Millions per request</b>
    <span class="wv-ex">every ad · every document</span>
  </div>
  <span class="wv-arrow" aria-hidden="true"></span>
  <div class="wv-node wv-node--rules">
    <span class="wv-eyebrow">Rules · gate</span>
    <b>Discard almost everything</b>
    <span class="wv-ex">targeting · budget · privacy · live permissions</span>
  </div>
  <span class="wv-arrow" aria-hidden="true"></span>
  <div class="wv-node wv-node--out">
    <span class="wv-eyebrow">Shortlist</span>
    <b>The few that survive</b>
  </div>
  <span class="wv-arrow" aria-hidden="true"></span>
  <div class="wv-node wv-node--model">
    <span class="wv-eyebrow">Model</span>
    <b>Ranks the survivors</b>
    <span class="wv-ex">the expensive step, run last</span>
  </div>
</div>
<p class="wv-cap"><b>The cheap rules gate runs first, so the expensive model only ever sees the shortlist.</b> <span class="wv-inw">In Winnow: matching one event against millions of eligibility rules in microseconds is its native job.</span></p>
</figure>

- **Snap's ad system** makes "trillions of predictions every day," but the
  first stage of its funnel is pure rules: targeting, budget, privacy, and
  policy checks decide which of millions of ads are even eligible before any
  model scores a candidate
  ([Snap engineering](https://eng.snap.com/machine-learning-snap-ad-ranking)).
- **Meta's ad delivery** works the same way: only ads whose advertiser-chosen
  targeting includes the person enter the auction; the machine-learning ranking
  runs on what survives
  ([Meta](https://www.facebook.com/business/news/good-questions-real-answers-how-does-facebook-use-machine-learning-to-deliver-ads)).
- The AI-search version of the pattern: **Glean's** enterprise AI assistant
  filters every document its retrieval step finds through the querying user's
  live permissions, so the model can only see (and quote) what that user is
  allowed to see
  ([Glean](https://www.glean.com/blog/secure-generative-ai-for-the-enterprise-requires-the-right-permissions-structure)).
  And **Pinecone** found that evaluating attribute filters *inside* vector
  search makes filtered queries **faster** than unfiltered ones (51.6 ms vs
  79.2 ms on a ~1.2M-record index)
  ([Pinecone](https://www.pinecone.io/learn/vector-search-filtering/)).

**In Winnow:** this gate is exactly Winnow's native job, matching one event
against millions of eligibility rules in microseconds (the
[ad-targeting](@/rule-engines/ad-targeting.md) percolation problem), so the
expensive model only ever sees the shortlist.

## 4. The model detects; the rules route

Detection systems produce scored suspicions; rules turn suspicion into a
manageable amount of human work: thresholds, suppression, escalation, routing.

<figure class="wv-figure">
<div class="wv-flow">
  <div class="wv-node wv-node--model">
    <span class="wv-eyebrow">Model</span>
    <b>Detects</b>
    <span class="wv-ex">scores suspicion on every event</span>
  </div>
  <span class="wv-arrow" aria-hidden="true"></span>
  <div class="wv-node wv-node--rules">
    <span class="wv-eyebrow">Rules · route</span>
    <b>Turn suspicion into work</b>
    <span class="wv-ex">threshold · suppress duplicates · escalate · in-scope only</span>
  </div>
  <span class="wv-arrow" aria-hidden="true"></span>
  <div class="wv-node wv-node--out">
    <span class="wv-eyebrow">Humans</span>
    <b>A few real cases</b>
    <span class="wv-ex">sized to investigator capacity</span>
  </div>
</div>
<p class="wv-cap"><b>HSBC: 2–4× more suspicious activity found with 60% fewer alerts.</b> <span class="wv-inw">In Winnow: “alert when the score crosses the threshold, for a covered service, outside a maintenance window” is a standing rule book</span>, with thresholds an operator can move while the stream keeps flowing.</p>
</figure>

- **HSBC** replaced blunt transaction-monitoring with Google Cloud's AML AI
  across **1.2 billion+ monthly transactions**: the model scores every customer
  for money-laundering risk, and a threshold policy (set to match investigator
  capacity) decides who gets investigated. Result: **2–4× more suspicious
  activity found with 60% fewer alerts**
  ([Google Cloud](https://cloud.google.com/blog/topics/financial-services/how-hsbc-fights-money-launderers-with-artificial-intelligence)).
- **Palo Alto Networks' own security operations** run on Cortex XSIAM: machine
  learning groups roughly **a trillion events a month** into incidents, and an
  automation layer of playbook rules closes out the noise, leaving about
  **eight incidents a day** for humans
  ([Palo Alto Networks](https://www.paloaltonetworks.com/blog/2022/10/xsiam-to-revolutionize-the-soc/)).
- In hospitals, **UC San Diego Health's** deep-learning sepsis model scores
  every emergency-department patient hourly, but a nurse is alerted only when
  the score crosses a set threshold *and* eligibility rules pass (patient still
  in the ED, treatment not already given). The combination cut sepsis deaths by
  **17% (relative)** in a two-hospital study
  ([npj Digital Medicine](https://pmc.ncbi.nlm.nih.gov/articles/PMC10805720/)).

**In Winnow:** "alert when the score crosses the threshold, for a covered service,
and not during a maintenance window" is a standing rule book evaluated per event: the
[real-time alerting](@/rule-engines/real-time-alerting.md) shape, with
thresholds an operator can move while the stream keeps flowing.

## 5. The rules keep AI agents on a leash

The newest and fastest-growing pattern. An AI *agent* doesn't just answer; it
acts: calls tools, moves money, edits records. Every single action needs a
policy decision, and one user request can fan out into dozens of actions.

<figure class="wv-figure">
<div class="wv-flow">
  <div class="wv-node wv-node--model wv-node--stack">
    <span class="wv-eyebrow">AI agent</span>
    <b>1 request → dozens of actions</b>
    <span class="wv-ex">calls tools · moves money · edits records</span>
  </div>
  <span class="wv-arrow" aria-hidden="true"></span>
  <div class="wv-node wv-node--rules">
    <span class="wv-eyebrow">Rules · every call</span>
    <b>Permit or forbid</b>
    <span class="wv-ex">who · what action · which resource · what context</span>
  </div>
  <span class="wv-arrow" aria-hidden="true"></span>
  <div class="wv-node wv-node--out">
    <span class="wv-eyebrow">Result</span>
    <b>Allowed action</b>
    <span class="wv-ex">or blocked, before it touches a real system</span>
  </div>
</div>
<p class="wv-cap"><b>Amazon Bedrock AgentCore checks every tool call in under a millisecond.</b> <span class="wv-inw">When agents act thousands of times a second, a millisecond check is the bottleneck; a microsecond one disappears.</span></p>
</figure>

- **Amazon's Bedrock AgentCore Gateway** checks every tool call an agent makes
  against explicit permit/forbid policies (who, what action, which resource, in
  what context) before the call reaches any real system, adding **under a
  millisecond** per check, with a policy change deployable by API call, no
  redeploy ([AWS](https://aws.amazon.com/blogs/machine-learning/secure-ai-agents-with-policy-and-lambda-interceptors-in-amazon-bedrock-agentcore-gateway/)).
- **Visa's Intelligent Commerce** lets AI agents find products and initiate
  payments, but only inside pre-defined spend controls and approval rules, on
  a network whose AI already scores every transaction in about a millisecond
  and blocks some **$40 billion in fraud a year**
  ([Visa](https://corporate.visa.com/en/solutions/intelligent-commerce/vcs-agentic-ai.html)).
- **Microsoft 365 Copilot** consults Purview data-protection rules on every
  prompt: content carrying certain sensitivity labels is excluded from what the
  AI may read or repeat, and an admin's policy edit reaches the whole tenant
  within hours; no model change involved
  ([Microsoft](https://learn.microsoft.com/en-us/purview/dlp-microsoft365-copilot-location-learn-about)).

**In Winnow:** an agent's tool call is one more event checked against a policy
book (the [access-control](@/rule-engines/access-control.md) shape), at machine
speed. When agents act thousands of times a second, a policy check that costs
milliseconds becomes the bottleneck; one that costs
[microseconds](@/rule-engines/cost-savings.md) disappears.

## 6. The rules run the AI factory

Rules don't just act on model outputs; they keep the machinery that *produces*
models honest.

<figure class="wv-figure">
<div class="wv-flow">
  <div class="wv-node wv-node--data">
    <span class="wv-eyebrow">The AI factory</span>
    <b>Data &amp; models pipeline</b>
    <span class="wv-ex">petabytes/day into hundreds of models</span>
  </div>
  <span class="wv-arrow" aria-hidden="true"></span>
  <div class="wv-node wv-node--rules">
    <span class="wv-eyebrow">Rules · keep it honest</span>
    <b>Validate · watch · gate</b>
    <span class="wv-ex">schema &amp; allowed values · drift monitors · rollout flags</span>
  </div>
  <span class="wv-arrow" aria-hidden="true"></span>
  <div class="wv-node wv-node--out">
    <span class="wv-eyebrow">Output</span>
    <b>Validated, safely shipped</b>
  </div>
</div>
<p class="wv-cap"><b>Rules don't just act on model outputs; they run the machinery that produces models.</b> <span class="wv-inw">In Winnow: schema constraints at ingest, threshold monitors over statistics, and feature-flag rollout gates are all built in.</span></p>
</figure>

- **Google's TFX platform** validates the several **petabytes of data a day**
  flowing into hundreds of production models against a declared schema of
  constraints: types, allowed values, expected distributions. One
  training-versus-serving mismatch it caught was worth a **2% install-rate
  improvement** on the Google Play homepage
  ([MLSys paper](https://mlsys.org/Conferences/2019/doc/2019/167.pdf)).
- **Uber's D3** watches the feature data behind production models (pricing
  included) with per-column monitors (null rates, percentiles, distributions)
  and cut the time to detect data problems by **more than 20×**
  ([Uber engineering](https://www.uber.com/en-GB/blog/d3-an-automated-system-to-detect-data-drifts/)).
- **OpenAI** rolled out ChatGPT features behind Statsig feature gates, rules
  deciding per user which model variant runs, scaling to hundreds of experiments
  across hundreds of millions of users, with an instant flag-flip as the
  rollback plan (OpenAI has since acquired Statsig)
  ([Statsig](https://www.statsig.com/)).

**In Winnow:** data validation at ingest is a built-in
([schema constraints](@/rule-engines/eligibility.md)); drift monitors are
threshold rules over statistics; rollout gates are the
[feature-flag](@/rule-engines/experimentation.md) shape.

## Why the rules half has to be fast

Notice what AI did to the rules workload in every story above. It didn't remove
it; it multiplied it:

<figure class="wv-figure">
<div class="wv-flow">
  <div class="wv-node wv-node--data">
    <span class="wv-eyebrow">Input</span>
    <b>1 request · 1 event</b>
  </div>
  <span class="wv-arrow" aria-hidden="true"></span>
  <div class="wv-node wv-node--model">
    <span class="wv-eyebrow">Model</span>
    <b>1 score</b>
  </div>
  <span class="wv-arrow" aria-hidden="true"></span>
  <div class="wv-node wv-node--rules wv-node--stack">
    <span class="wv-eyebrow">Rules · × dozens</span>
    <b>Many checks, on the hot path</b>
    <span class="wv-ex">every score, every agent action, every candidate</span>
  </div>
  <span class="wv-arrow" aria-hidden="true"></span>
  <div class="wv-node wv-node--out">
    <span class="wv-eyebrow">In Winnow</span>
    <b>~3 µs each</b>
    <span class="wv-ex">changed live, never a redeploy</span>
  </div>
</div>
<p class="wv-cap"><b>AI doesn't shrink the rules workload; it multiplies it.</b> <span class="wv-inw">That is the workload Winnow is built for:</span> model scores are ordinary columns, policy is plain editable text, and every check costs about three microseconds.</p>
</figure>

- **Every score is one more decision.** Each scored payment, post, patient, or
  alert now needs a policy evaluation on top of the model call.
- **Agents fan out.** One user request becomes dozens of tool calls, each
  needing a permission check on the hot path.
- **The funnel gate sees everything.** The pruning stage runs against *millions*
  of candidates per request, before the model does any work at all.
- **Policy moves faster than models.** Braze retrains every two weeks;
  Salesforce Einstein every ten days; HSBC's models retrain on a governed
  cadence. But when fraud spikes or a regulator calls, the *rules* change in
  minutes. The rules layer is where a business actually steers its AI.

That is the workload Winnow is built for: model scores are ordinary columns,
policy is plain editable text, every check costs
[about three microseconds](@/rule-engines/cost-savings.md), and rules change live
without pausing a single decision.

*Each use-case page in this section now carries a short "with AI in the loop"
note showing how these patterns land in that domain.*
