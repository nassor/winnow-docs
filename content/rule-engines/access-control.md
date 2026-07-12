+++
title = "Access control & entitlements"
description = "Decide who can do what from attributes and policy: evaluated on every request."
weight = 30

[extra]
theme = "risk"
+++

"Can this person do this, to this, right now?" Modern access control answers that
from **attributes and policy** rather than brittle, ever-growing lists of static
roles, and that decision is itself a rule evaluation on the critical path of every
request.

<div class="callout callout-perf">
<p class="callout-title">⚡ Where speed pays for itself</p>
<p>Authorization runs on <em>every</em> request. Even a few extra milliseconds, multiplied across billions of calls, is a speed tax on the whole product and a larger server bill. Winnow decides in microseconds, inside your own service: no per-request trip to a policy server, no fleet to scale, so the cost of enforcing access stays near zero however much traffic grows.</p>
</div>

## The stakes

Access is where breaches happen. In Verizon's analysis, **stolen credentials appear
in ~38% of breaches** and **68% involve a human element**
([Verizon DBIR 2024](https://www.securitymagazine.com/articles/100629-verizon-2024-data-breach-report-shows-the-risk-of-the-human-element)).
Static role lists drift and over-grant over time. **Attribute-based access control
(ABAC)** decides each request from the subject, the resource, and the context,
which means an engine has to evaluate a policy, correctly and fast, on *every* call.

## How it works

A policy is a rule over the requester, the resource, and the context. For example,
*"allow when the user is in department 4, has clearance level 3 or higher, the
resource is class 7, and the user is not a contractor."* Rules can also compare one
attribute against another (the level requested against the level granted, say). Every access
request is evaluated against the policy, and the policy is editable as plain text.

<div class="callout callout-ai">
<p class="callout-title">🤖 With AI in the loop</p>
<p>AI agents have turned access control into a machine-speed problem: an agent
doesn't just answer, it <em>acts</em>, and every tool call needs a permission
decision. <a href="https://aws.amazon.com/blogs/machine-learning/secure-ai-agents-with-policy-and-lambda-interceptors-in-amazon-bedrock-agentcore-gateway/">Amazon's Bedrock AgentCore Gateway</a>
checks each agent tool call against permit/forbid policies (who, what action,
which resource, in what context) in under a millisecond, with policy changes
deployed by API call. <a href="https://learn.microsoft.com/en-us/purview/dlp-microsoft365-copilot-location-learn-about">Microsoft 365 Copilot</a>
consults Purview rules on every prompt so labeled-sensitive content is excluded
from what the AI may read or repeat. <a href="https://www.glean.com/blog/secure-generative-ai-for-the-enterprise-requires-the-right-permissions-structure">Glean</a>
filters everything its AI assistant retrieves through the asking user's live
permissions. One user request can fan out into dozens of agent actions, so a
policy check that costs milliseconds becomes the product's bottleneck; one that
costs <a href="../cost-savings/">microseconds</a> disappears. More
patterns: <a href="../ai-and-rules-engines/">Deep Dive: AI + Rules Engines</a>.</p>
</div>

## Why Winnow fits

- **Attributes are first-class:** department, role, and resource class are
  category checks; clearance and limits are
  number checks; conditions combine with `AND` / `OR` /
  `NOT`; and a request's attributes can be compared directly against a resource's.
- **On the request path:** microsecond decisions, made inside your own service, so
  there is no extra hop to a policy server before every action.
- **Revoke and grant live:** change a policy by editing rules; requests already in
  flight still see one complete, consistent policy,
  and nothing restarts.
- **Both directions:** check one request against many policies, or audit the whole
  population (*"who can currently access resource class 7?"*) in one pass.
