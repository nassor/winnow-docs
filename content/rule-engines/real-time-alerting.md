+++
title = "Real-time alerting & monitoring"
description = "Match a firehose of events and metrics against alert rules in real time, and keep the signal-to-noise ratio high."
weight = 40

[extra]
theme = "risk"
+++

Operations, security, and observability all run on the same pattern: a stream of
events and metrics, matched against a set of conditions that decide what is worth a
human's attention. Get the rules right and incidents surface early; get them wrong
and the team tunes out.

<div class="callout callout-perf">
<p class="callout-title">⚡ Where speed pays for itself</p>
<p>The input is a firehose. If rule evaluation can't keep pace, the only options are to sample (and miss incidents) or to buy ever more servers for the evaluation tier. Matching each event against the whole rulebook in microseconds processes the full stream on a small fraction of the hardware, delivering full coverage without an ever-growing compute bill.</p>
</div>

## The stakes

Alert noise is the dominant failure mode. Studies find roughly **80%+ of security
alerts are false alarms**, the average operations center handles on the order of
**960 alerts a day**, and about **42% of alerts go uninvestigated** for lack of time
([Vectra](https://www.vectra.ai/topics/alert-fatigue),
[Abnormal AI](https://abnormal.ai/learning/alert-fatigue-cybersecurity)). The fix is
sharper rules: precise conditions that fire only when several signals line up, and
an engine fast enough to evaluate them on every event.

## How it works

Each alert condition is a rule, evaluated against every incoming event or metric
sample. For example, *"raise an alert when metric 7 (say, error rate ×100) exceeds
95% on service 3 or 8, and we are not in a maintenance window."* This is a reverse query:
one event tested against many alert rules at once, returning the rules that fire.

<div class="callout callout-ai">
<p class="callout-title">🤖 With AI in the loop</p>
<p>AI detection has made the <em>routing</em> rules more important, not less.
<a href="https://www.paloaltonetworks.com/blog/2022/10/xsiam-to-revolutionize-the-soc/">Palo Alto Networks' own security team</a>
uses machine learning to group roughly a trillion events a month, then playbook
rules close out the noise, leaving about <strong>eight incidents a day</strong>
for humans. <a href="https://www.microsoft.com/en-us/security/blog/2020/02/20/azure-sentinel-uncovers-real-threats-hidden-billions-low-fidelity-signals/">Microsoft Sentinel's Fusion</a>
correlated nearly 50 billion anomalous alerts in a month into 25 actionable
incidents; in <a href="https://www.elastic.co/docs/solutions/security/detect-and-alert/machine-learning">Elastic Security</a>,
anomaly models emit 0–100 scores and detection rules alert only above a
user-set threshold, with suppression rules grouping duplicates. In Winnow the
anomaly score is a column and the entire triage policy (thresholds,
suppression windows, routing) is editable rules evaluated per event at stream
speed. More patterns: <a href="../ai-and-rules-engines/">Deep Dive: AI + Rules Engines</a>.</p>
</div>

## Why Winnow fits

- **Event-vs-rules at speed:** a single event is matched against the whole rule set
  in microseconds, so evaluation keeps up with a high-volume stream.
- **Combined conditions cut noise:** thresholds are number checks,
  hosts/services/severities are category checks, and
  `AND` / `OR` / `NOT` express "alert only when X *and* Y *and not* Z," the
  multi-signal rules that reduce false alarms.
- **Suppression and routing for free:** the same boolean rules express maintenance
  windows, dedup conditions, and which team an alert routes to.
- **Edit without redeploying:** on-call engineers adjust thresholds and silence
  rules as text, applied live with no restart.
