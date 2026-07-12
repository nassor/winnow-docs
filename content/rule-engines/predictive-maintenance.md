+++
title = "IoT & predictive maintenance"
description = "Match a stream of sensor readings against condition rules to catch failures before they happen."
weight = 120

[extra]
theme = "ops"
+++

Connected equipment emits a constant stream of readings: temperature, vibration,
pressure, cycle counts. Turning that stream into action is a rule-matching problem:
which assets are showing the patterns that precede a failure, right now?

<div class="callout callout-perf">
<p class="callout-title">⚡ Where speed pays for itself</p>
<p>Telemetry arrives continuously and per-reading. Evaluating rules fast enough to run at the edge (inside a gateway or on-prem service, with no cloud round-trip per reading) cuts both bandwidth and central compute: decisions are made where the data is, on modest hardware, and only the alerts travel onward.</p>
</div>

## The stakes

Catching problems early pays off heavily. Deloitte and McKinsey analyses find
predictive maintenance can **cut unplanned downtime by 30–50%** and **maintenance
costs by 18–30%**
([Deloitte](https://www.deloitte.com/us/en/insights/industry/manufacturing-industrial-products/industry-4-0/using-predictive-technologies-for-asset-maintenance.html)),
against a backdrop where unplanned downtime is estimated to cost industrial
manufacturers on the order of **$50 billion a year**. The rules that flag at-risk
assets have to run against a high-volume sensor stream without falling behind.

## How it works

Each failure signature is a rule, evaluated against incoming readings. For example,
*"flag assets running above 85°C (stored ×100) with elevated vibration, in machine
classes 2 or 5, that are more than 2,000 hours past service."* Sensor thresholds and
counters are number checks; asset class and site are
category checks. A rule can also compare one
reading against another: a live reading against its rolling baseline, say.

<div class="callout callout-ai">
<p class="callout-title">🤖 With AI in the loop</p>
<p>Industrial AI pairs learned models with engineering rules.
<a href="https://www.gevernova.com/software/products/asset-performance-management/equipment-downtime-predictive-analytics">GE Vernova's SmartSignal</a>
uses digital-twin models to flag anomalies across 19,500+ monitored assets, then
matches them against a library of failure-precursor rules and alarm limits to
decide what becomes a work order. <a href="https://www.ingenia.org.uk/articles/engine-health-managment/">Rolls-Royce's engine health monitoring</a>
trends per-flight sensor data with neural networks while hard-limit rules fire
in real time on the engine. The combination mitigated 75% of potential
in-flight events in a study year. On inspection lines,
<a href="https://landinglens.docs.landing.ai/view-predictions">Landing AI's</a>
vision models emit a confidence score per defect, and a threshold rule decides
accept or reject; moving that threshold is an instant policy change, no
retraining. In Winnow, model outputs (anomaly scores, confidences, forecasts)
are columns; the alarm limits, persistence conditions, and work-order triggers
around them are rules your engineers own. More patterns:
<a href="../ai-and-rules-engines/">Deep Dive: AI + Rules Engines</a>.</p>
</div>

## Why Winnow fits

- **Sensor data is numeric and bursty:** threshold checks
  sweep thousands of assets per pass, and "above its own baseline" rules compare one
  reading directly against another.
- **Keeps up with the stream:** microsecond evaluation, applied to whole batches of
  readings at once, scales to high-frequency telemetry without a per-reading round
  trip.
- **Embeddable at the edge:** a self-contained component with no server dependency
  runs inside a gateway or on-prem service, close to the equipment.
- **Tunable signatures:** maintenance engineers refine thresholds as they learn an
  asset's behavior, editing rules as text and applying them live.
