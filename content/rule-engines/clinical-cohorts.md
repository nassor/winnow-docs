+++
title = "Clinical cohorts & trial matching"
description = "Find the patients who meet a set of clinical criteria: across millions of records, or one patient against every trial."
weight = 110

[extra]
theme = "ops"
+++

"Which patients meet these criteria?" is a rule-matching question, and it sits at
the center of clinical research, quality measurement, and decision support.
Eligibility criteria are conditions over labs, diagnoses, demographics, and notes,
exactly the mix a fast filter engine is built for.

## The stakes

Patient recruitment is the chronic bottleneck of clinical research: roughly **80% of
trials fail to meet their enrollment timeline**, and recruitment and retention are
the cause of about **80% of trial delays**
([Power](https://www.withpower.com/guides/enrollment-in-clinical-trials-statistics-and-patient-recruitment-strategies)).
Those delays are expensive. Estimates run from **$600,000 to $8 million for every
day** a trial is held up
([Applied Clinical Trials](https://www.appliedclinicaltrialsonline.com/view/inside-recruitment-bottlenecks-driving-trial-delays)).
Finding eligible patients faster, against criteria that are easy to adjust, moves
that number directly.

## How it works

Eligibility criteria become a rule evaluated against the patient population. For
example, *"patients aged 40–75 with HbA1c above 7.0% (stored ×10), carrying
diagnosis code 250 or 401, and not on the excluded medication 88."* Lab values and age are
number checks; diagnosis and medication codes are
category checks; free text in clinical notes
is matched by text search.

<div class="callout callout-ai">
<p class="callout-title">🤖 With AI in the loop</p>
<p>Clinical AI ships wrapped in rules. <a href="https://pmc.ncbi.nlm.nih.gov/articles/PMC10805720/">UC San Diego Health's</a>
deep-learning sepsis model scores every emergency-department patient hourly, but
a nurse is alerted only when the score crosses a set threshold <em>and</em>
eligibility rules pass (patient still in the ED, treatment not already given),
a combination that cut sepsis deaths 17% (relative) across two hospitals. The
<a href="https://www.nature.com/articles/s41591-022-01894-0">TREWS early-warning system</a>
adds a provider-confirmation rule before any alert drives treatment. For trial
matching, <a href="https://pmc.ncbi.nlm.nih.gov/articles/PMC11574183/">NIH's TrialGPT</a>
puts fast deterministic retrieval in front of the language model (pruning
~26,000 trials to ~500 before the model reads anything) and cut screening time
by 42.6%. In Winnow, model scores and AI-extracted fields are columns; the
thresholds, eligibility gates, and suppression logic around them are rules
clinicians can read. More patterns:
<a href="../ai-and-rules-engines/">Deep Dive: AI + Rules Engines</a>.</p>
</div>

## Why Winnow fits

- **The clinical data model maps cleanly:** lab values as number checks, ICD/ATC
  codes as category checks, notes as searchable text,
  with exclusions expressed by `NOT`.
- **Cohort building in one pass:** evaluate the criteria across millions of patient
  records at once to assemble a cohort.
- **One patient, many trials:** run the question in reverse, matching a single patient
  against hundreds of trial-eligibility rules in microseconds, surfacing every study
  they qualify for at the point of care.
- **Live data, stable answers:** criteria and patient records can change while a
  search runs; every search sees one complete, consistent view
  of the data.

> Winnow evaluates the criteria; it is not a medical device and makes no clinical
> determination. A clinician reviews the candidates it surfaces.
