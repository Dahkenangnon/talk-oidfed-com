---
marp: true
theme: oidfed
paginate: true
size: 16:9
title: "@oidfed implementation overview & feedback"
description: "@oidfed — the OpenID Federation 1.0 implementation for JavaScript. Presented at the OIDF AB/Connect Working Group, 2026-05-21."
header: "@oidfed implementation overview & feedback"
footer: "@oidfed · OIDF AB/Connect WG · 2026-05-21 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; oidfed.com"
html: true
---

<!-- _class: cover -->

<div class="hero">

# @oidfed

<p class="lede">A complete OpenID Federation&nbsp;1.0 implementation for JavaScript.</p>

</div>

<div class="image">

![oidfed entity statement anatomy — header, claims, signature](assets/oidfed-slide-intro.png)

</div>

<div class="meta">
  <div class="name">Justin Dah-Kenangnon</div>
  <div>
    <a href="https://github.com/Dahkenangnon">github.com/Dahkenangnon</a>
    <span class="dot">·</span>
    <a href="mailto:dah.kenangnon@gmail.com">dah.kenangnon@gmail.com</a>
    <span class="dot">·</span>
    <a href="https://www.linkedin.com/in/dahkenangnon/">https://www.linkedin.com/in/dahkenangnon/</a>
  </div>
</div>

---

## Justin Yamonwan Dah-kenangnon

<ul class="body-bullets">
  <li><strong>Background</strong> — Engineering graduate in Mathematics &amp; Modeling<em>software engineer with a focus on identity and integration work</em></li>
  <li><strong>OIDC</strong> — integrations mostly built on Filip Skokan's <code>node-oidc-provider</code><em>deploying real OPs and consuming them as RPs</em></li>
  <li><strong>Federation</strong> — came in cold to the OpenID Federation spec<em>this implementation is the way I learned it — the source of today's feedback</em></li>
</ul>

---

## What @oidfed is

<ul class="pkgs">
  <li><strong>@oidfed/core</strong><span>Entity Statements, trust&nbsp;chain resolution, metadata policy, crypto verification</span></li>
  <li><strong>@oidfed/authority</strong><span>Trust Anchor &amp; Intermediate — subordinate mgmt, issuance, endpoints, policy</span></li>
  <li><strong>@oidfed/leaf</strong><span>Leaf Entity toolkit — EC serving, authority discovery, chain participation</span></li>
  <li><strong>@oidfed/oidc</strong><span>OIDC / OAuth&nbsp;2.0 flows — automatic &amp; explicit registration, Request Object</span></li>
  <li><strong>@oidfed/cli</strong><span>Resolve, fetch, validate, decode, sign — debug deployments from the terminal</span></li>
</ul>

<h4 style="margin-top:24px">Apps &amp; resources</h4>

<div class="demo-apps">

<div class="app">
  <div class="head">
    <span class="name">fed.oidfed.com</span>
    <span class="demo-tag">Demo · not production</span>
  </div>
  <p class="desc">Reference federation deployment — six topologies built on the @oidfed packages, exercised end-to-end to validate spec compliance.</p>
</div>

<div class="app">
  <div class="head">
    <span class="name">explore.oidfed.com</span>
  </div>
  <p class="desc">Browser-based federation explorer — paste any entity URL, render its trust chain, statements, and resolved metadata inline.</p>
</div>

<div class="app resources">
  <div class="head">
    <span class="name">resources</span>
  </div>
  <p class="desc">learn.oidfed.com &nbsp;·&nbsp; oidfed.com<br>course material and project home.</p>
</div>

</div>

---

## The demo federation

<span class="demo-tag">Demo · validates spec compliance · not real-world adoption</span>

<ul class="body-bullets" style="margin-top:18px">
  <li><strong>Six topologies live</strong> — single-anchor · hierarchical · multi-anchor · cross-federation · constrained · policy-operators<em>each one exercises a different corner of the spec on shared @oidfed/* code</em></li>
  <li><strong>One Node process</strong> — vhost-dispatched · Hono for federation-only entities · Express + <code>node-oidc-provider</code> for OPs<em>same source tree mirrors the upstream e2e bed at <code>oidfed/tests/e2e/</code></em></li>
</ul>

<div class="test-card">
  <span class="l">Browser</span>
  <span class="v">Sign in at <code>rp1.single.fed.oidfed.com</code> or <code>rp2.single.fed.oidfed.com</code></span>
  <span class="l">CLI</span>
  <span class="v"><code>oidfed resolve https://single.fed.oidfed.com</code> &nbsp;·&nbsp; <code>oidfed chain &lt;leaf&gt; --ta &lt;ta&gt;</code></span>
  <span class="l">Explorer</span>
  <span class="v">Paste any entity URL into <code>explore.oidfed.com</code> — chain &amp; metadata rendered live</span>
</div>

---

## Live demo

<span class="demo-tag">Demo federation · not production</span>

<ol class="demo-list" style="margin-top:18px">
  <li><code>rp1.single.fed.oidfed.com</code> — <strong>automatic</strong> client registration</li>
  <li><code>rp2.single.fed.oidfed.com</code> — <strong>explicit</strong> client registration</li>
  <li><code>explore.oidfed.com</code> — the same federation, visualised</li>
</ol>

<pre class="ex" style="margin-top:18px;max-width:880px"># Inspect any participant straight from the terminal — no install needed:
$ npx -y -p @oidfed/cli oidfed entity https://op.single.fed.oidfed.com</pre>

---

## Three pieces of feedback
### From running the implementation

<div class="fb-overview">

<div class="item">
  <span class="num">Item 1</span>
  <span class="title">§12.1.1.1.1 ·<br>request delivery</span>
  <p class="one-liner">Embedded <code>trust_chain</code> did not fit the query carrier in our deployment — sharing what we saw.</p>
</div>

<div class="item">
  <span class="num">Item 2</span>
  <span class="title">§6.2 ·<br>constraints side</span>
  <p class="one-liner">Unknown constraint parameters are silently ignored — a gap compared to the metadata side.</p>
</div>

<div class="item">
  <span class="num">Item 3</span>
  <span class="title">Runtime policy ·<br>when inputs change</span>
  <p class="one-liner">Decisions whose inputs change between requests — sharing a need we ran into.</p>
</div>

</div>

---

## Feedback&nbsp;1 &nbsp;·&nbsp; §12.1.1.1.1
### Request delivery &nbsp;·&nbsp; Observation &amp; Description

<div class="fbd">

<span class="lbl lbl-issue">Observation</span>
<div class="row">In our deployment, an embedded <code>trust_chain</code> in the Request Object's JWS header pushes the <code>?request=&lt;JWT&gt;</code> query carrier past common HTTP-intermediary defaults.</div>

<span class="lbl">Description</span>
<div class="row">A three-statement chain (Leaf + Intermediate + TA) is already 8–12&nbsp;KB — past nginx's default <code>proxy_buffer_size</code> of 8&nbsp;KB. The 502 fired on the <strong>smallest interoperable topology</strong>, not on a deep edge case.<br><br>The spec already names the carriers that handle this in §12.1.1.1.1 — <em>"it may be necessary to use the HTTP POST method, a request_uri, or PAR for the request."</em> In our deployment, that note turned out to describe the default path, not an edge case.</div>

</div>

---

## Feedback&nbsp;1 &nbsp;·&nbsp; §12.1.1.1.1
### Request delivery &nbsp;·&nbsp; Suggestion &amp; Example

<div class="fbd">

<span class="lbl lbl-proposal">Suggestion</span>
<div class="row">A clearer note on this — saying the query carrier is risky when <code>trust_chain</code> is embedded, and pointing readers to <code>form_post</code>, <code>request_uri</code>, or PAR — would save the next implementer the same 502.<br><br>No change to OIDC Core's request parameter; the carrier choice only matters when the chain is in the header.</div>

<span class="lbl">Example</span>
<div class="row">
<pre class="ex">// Failing path
GET /authorize?request=&lt;9KB JWT with trust_chain&gt;
→ HTTP/1.1 502 Bad Gateway   (nginx proxy_buffer_size)
&nbsp;
// Working path (form_post default in @oidfed/oidc@0.4.0)
POST /authorize  Content-Type: application/x-www-form-urlencoded
request=&lt;same JWT&gt;</pre>
</div>

</div>

---

## Feedback&nbsp;2 &nbsp;·&nbsp; §6.2
### Constraints-side parallel &nbsp;·&nbsp; Observation &amp; Description

<div class="fbd">

<span class="lbl lbl-issue">Observation</span>
<div class="row">§6.2 lets federations define custom constraint parameters and requires <strong>silent-ignore</strong> for unrecognised ones. There is no fail-closed equivalent on the constraint side.</div>

<span class="lbl">Description</span>
<div class="row">The metadata side already has <code>metadata_policy_crit</code> (§6.1.3.2) — a federation can mark a custom operator as required-to-understand, with chain invalidity as the failure mode.<br><br>On the constraint side there is no equivalent today. A verifier that hasn't implemented a custom constraint silently admits the chain, even when the federation operator considers that constraint essential.</div>

</div>

---

## Feedback&nbsp;2 &nbsp;·&nbsp; §6.2
### Constraints-side parallel &nbsp;·&nbsp; Suggestion &amp; Example

<div class="fbd">

<span class="lbl lbl-proposal">Suggestion</span>
<div class="row">A <code>constraints_crit</code> claim that mirrors <code>metadata_policy_crit</code> semantics would close this gap. One new claim, no change to existing ones.</div>

<span class="lbl">Example</span>
<div class="row">
<pre class="ex">// Subordinate Statement
{
  "iss": "https://ta.regulated-fed.example",
  "sub": "https://intermediate.bank.example",
  "constraints": {
    "max_path_length": 2,
    "required_trust_marks_in_chain": ["https://.../audit-2026"]
  },
  "constraints_crit": ["required_trust_marks_in_chain"]
}</pre>
</div>

</div>

---

## Feedback&nbsp;3 &nbsp;·&nbsp; runtime policy
### When inputs change between requests &nbsp;·&nbsp; Observation &amp; Description

<div class="fbd">

<span class="lbl lbl-issue">Observation</span>
<div class="row">§6's extension points (operators, constraints) are made to be shared as code and stay static. They aren't shaped to carry decisions whose inputs change <strong>between requests</strong> — entity-graph state, federation-operational mode, dynamic naming.</div>

<span class="lbl">Description</span>
<div class="row">Real cases we ran into: delegation budgets that should narrow during incidents, federation-wide capacity limits, entity-reliability numbers over sliding windows.<br><br>§6.1.1 <em>Determinism</em> rightly keeps these out of static metadata-policy — so this kind of decision can't live inside <code>metadata_policy</code>. Federations that need it today have to build it outside the chain, and lose the federation's signature on the result.</div>

</div>

---

## Feedback&nbsp;3 &nbsp;·&nbsp; runtime policy
### When inputs change between requests &nbsp;·&nbsp; Direction

<div class="fbd">

<span class="lbl lbl-proposal">Direction</span>
<div class="row">The shape we have in mind is an opt-in <code>federation_policy_evaluation_endpoint</code> that sits next to the static §6 layer — it never replaces it. The endpoint returns a signed decision with <code>iat</code>/<code>exp</code>, so cached decisions stay deterministic inside the window.<br><br>The decision can only narrow what the static layer already allows; it can never loosen it — §6.1.1 Hierarchy preserved.</div>

<span class="lbl">Question</span>
<div class="row">Just one: <em>"Does the underlying need feel real enough for the WG to look at it further?"</em></div>

</div>

---

## Feedback&nbsp;3 &nbsp;·&nbsp; runtime policy
### When inputs change between requests &nbsp;·&nbsp; Example

<div class="fbd">

<span class="lbl">TA declares the endpoint</span>
<div class="row">
<pre class="ex">"metadata": {
  "federation_entity": {
    "federation_policy_evaluation_endpoint":
      "https://policy.ai-fed.example/evaluate",
    "federation_policy_evaluation_fallback": "use_cached"
  }
}</pre>
</div>

<span class="lbl">Signed decision</span>
<div class="row">
<pre class="ex">// policy-decision+jwt — narrows a static budget federation-wide
{ "iss": "https://policy.ai-fed.example",
  "sub": "https://advisor-platform.example",
  "iat": 1779373800,  "exp": 1779373860,
  "decision": "modified",
  "effective_metadata": { "default_delegation_budget": 50 } }</pre>
</div>

</div>

---

<!-- _class: closing -->

# Thank you.

<p class="open-line">Open to any suggestion, question, or further involvement — on the spec, on the implementation, or on the demo.</p>

<div class="contact-line">
  <span class="name">Justin Dah-Kenangnon</span>
  <a href="mailto:dah.kenangnon@gmail.com">dah.kenangnon@gmail.com</a>
  <span class="sep">·</span>
  <a href="https://github.com/Dahkenangnon">github.com/Dahkenangnon</a>
  <span class="sep">·</span>
  <a href="https://www.linkedin.com/in/dahkenangnon/">https://www.linkedin.com/in/dahkenangnon/</a>
</div>

<div class="after-card">
  <span class="heading">After WG Call</span>
  <div class="urls">
    <a href="https://talk.oidfed.com/openid-working-group-call-2026-05-21/">talk.oidfed.com/openid-working-group-call-2026-05-21</a>
    <span class="sep">·</span>
    <a href="https://fed.oidfed.com">fed.oidfed.com</a>
    <span class="sep">·</span>
    <a href="https://explore.oidfed.com">explore.oidfed.com</a>
    <span class="sep">·</span>
    <a href="https://learn.oidfed.com">learn.oidfed.com</a>
    <span class="sep">·</span>
    <a href="https://github.com/Dahkenangnon/oidfed">github.com/Dahkenangnon/oidfed</a>
  </div>
</div>
