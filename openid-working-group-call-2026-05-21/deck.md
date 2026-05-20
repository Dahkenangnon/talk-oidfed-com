---
marp: true
theme: oidfed
paginate: true
size: 16:9
title: "@oidfed implementation overview & feedback"
description: "@oidfed — the OpenID Federation 1.0 implementation for JavaScript. Presented at the OIDF AB/Connect Working Group, 2026-05-21."
header: "@oidfed implementation overview & feedback"
footer: "@oidfed · OIDF AB/Connect WG · 2026-05-21 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; talk.oidfed.com"
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
### ≈ 90 seconds

<span class="demo-tag">Demo federation · not production</span>

<ol class="demo-list" style="margin-top:18px">
  <li><code>rp1.single.fed.oidfed.com</code> — <strong>automatic</strong> client registration</li>
  <li><code>rp2.single.fed.oidfed.com</code> — <strong>explicit</strong> client registration</li>
  <li><code>explore.oidfed.com</code> — the same federation, visualised</li>
</ol>

---

## Three pieces of feedback
### From running the implementation

<div class="fb-overview">

<div class="item">
  <span class="num">Item 1</span>
  <span class="title">§12.1.1.1.1 ·<br>request delivery</span>
  <p class="one-liner">Embedded <code>trust_chain</code> overflows the query carrier — propose a conditional <code>SHOULD&nbsp;NOT</code>.</p>
</div>

<div class="item">
  <span class="num">Item 2</span>
  <span class="title">§6.2 ·<br>constraints_crit</span>
  <p class="one-liner">No fail-closed escape hatch for custom constraints — add a mirror of <code>metadata_policy_crit</code>.</p>
</div>

<div class="item">
  <span class="num">Item 3</span>
  <span class="title">Runtime policy ·<br>future-work signal</span>
  <p class="one-liner">Decisions whose inputs change between requests — candidate for a separate I-D, not a §6 amendment.</p>
</div>

</div>

---

## Feedback&nbsp;1 &nbsp;·&nbsp; §12.1.1.1.1
### Request delivery &nbsp;·&nbsp; Issue &amp; Description

<div class="fbd">

<span class="lbl lbl-issue">Issue</span>
<div class="row">Embedded <code>trust_chain</code> in the Request Object's JWS header overflows the <code>?request=&lt;JWT&gt;</code> query carrier.</div>

<span class="lbl">Description</span>
<div class="row">A three-statement chain (Leaf + Intermediate + TA) is already 8–12&nbsp;KB — past nginx's default <code>proxy_buffer_size</code> of 8&nbsp;KB. The 502 fires on the <strong>smallest interoperable topology</strong>, not on a deep edge case.<br><br>The spec already acknowledges the size pressure in §12.1.1.1.1 — <em>"it may be necessary to use the HTTP POST method, a request_uri, or PAR for the request"</em> — but reads as a footnote rather than guidance for the production path.</div>

</div>

---

## Feedback&nbsp;1 &nbsp;·&nbsp; §12.1.1.1.1
### Request delivery &nbsp;·&nbsp; Proposal &amp; Example

<div class="fbd">

<span class="lbl lbl-proposal">Proposal</span>
<div class="row">Add a conditional <code>SHOULD&nbsp;NOT</code> to §12.1.1.1.1 when <code>trust_chain</code> is embedded. POST <code>form_post</code>, <code>request_uri</code>, or PAR <code>SHOULD</code> be used instead.<br><br>No default change to OIDC Core's request-parameter shape — only conditional, only when the chain is in the header.</div>

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
### Add `constraints_crit` &nbsp;·&nbsp; Issue &amp; Description

<div class="fbd">

<span class="lbl lbl-issue">Issue</span>
<div class="row">§6.2 lets federations define custom constraint parameters but mandates <strong>silent-ignore</strong> for unrecognised ones. No fail-closed escape hatch on the constraint side.</div>

<span class="lbl">Description</span>
<div class="row">The metadata side already has <code>metadata_policy_crit</code> (§6.1.3.2) — a federation can mark a custom operator as required-to-understand, with chain invalidity as the failure mode.<br><br>The constraint side has nothing equivalent. A verifier that hasn't implemented a custom constraint silently admits the chain, breaking the federation's stated guarantee.</div>

</div>

---

## Feedback&nbsp;2 &nbsp;·&nbsp; §6.2
### Add `constraints_crit` &nbsp;·&nbsp; Proposal &amp; Example

<div class="fbd">

<span class="lbl lbl-proposal">Proposal</span>
<div class="row">Add a <code>constraints_crit</code> claim to §3.1.3 — exact mirror of <code>metadata_policy_crit</code> semantics. One new claim name, backwards-compatible, low review burden.</div>

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
### Future-work signal &nbsp;·&nbsp; Issue &amp; Description

<div class="fbd">

<span class="lbl lbl-issue">Issue</span>
<div class="row">§6's extension points (operators, constraints) are code-distributable and static. They cannot encode decisions whose inputs change <strong>between requests</strong> — entity-graph state, federation-operational mode, dynamic naming.</div>

<span class="lbl">Description</span>
<div class="row">Concrete drivers: delegation budgets that narrow during operational incidents, federation-wide capacity envelopes, entity-reliability metrics over sliding windows.<br><br>§6.1.1 <em>Determinism</em> correctly rules these out of static metadata-policy — so the layer cannot live inside <code>metadata_policy</code>. Federations today are forced to fork the operator set or build the policy layer out-of-band, losing federation's signature guarantees.</div>

</div>

---

## Feedback&nbsp;3 &nbsp;·&nbsp; runtime policy
### Future-work signal &nbsp;·&nbsp; Proposal

<div class="fbd">

<span class="lbl lbl-proposal">Proposal</span>
<div class="row">Not a §6 amendment. Candidate for a separate I-D — working title <code>oidf-runtime-policy-1_0</code> — defining an opt-in <code>federation_policy_evaluation_endpoint</code> alongside the static layer.<br><br>Signed verdict carries <code>iat</code>/<code>exp</code>, so cached decisions remain deterministic inside the window. The runtime layer MAY further restrict; it MUST NOT loosen the static one — §6.1.1 Hierarchy preserved.</div>

<span class="lbl">Ask</span>
<div class="row">Just one: <em>"Is the underlying need real enough to merit a draft I-D, and would the WG entertain it as separate work in the post-1.1 follow-up cycle?"</em></div>

</div>

---

## Feedback&nbsp;3 &nbsp;·&nbsp; runtime policy
### Future-work signal &nbsp;·&nbsp; Example

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

<span class="lbl">Signed verdict</span>
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
