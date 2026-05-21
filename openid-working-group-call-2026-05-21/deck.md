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
  <div class="name">Yamonwan Justin Dah-kenangnon</div>
  <div>
    <a href="https://github.com/Dahkenangnon">github.com/Dahkenangnon</a>
    <span class="dot">·</span>
    <a href="mailto:dah.kenangnon@gmail.com">dah.kenangnon@gmail.com</a>
    <span class="dot">·</span>
    <a href="https://www.linkedin.com/in/dahkenangnon/">https://www.linkedin.com/in/dahkenangnon/</a>
  </div>
</div>

---

## Yamonwan Justin Dah-kenangnon

<div class="speaker-grid">

<div class="speaker-bio">

<ul class="body-bullets">
  <li><strong>Background</strong> — Engineering graduate in Mathematics &amp; Modeling<em>software engineer with a focus on identity and integration work</em></li>
  <li><strong>OIDC</strong> — integrations mostly built on Filip Skokan's <code>node-oidc-provider</code><em>deploying real OPs and consuming them as RPs</em></li>
  <li><strong>Federation</strong> — came in cold to the OpenID Federation spec<em>this implementation is the way I learned it — the source of today's feedback</em></li>
</ul>

</div>

<aside class="speaker-portrait">
  <img src="assets/yjdk-avatar.jpg" alt="Yamonwan Justin Dah-kenangnon">
  <p class="handle">@Dahkenangnon</p>
  <p class="moto">maths · code · internet standards</p>
</aside>

</div>

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

## What each library runs
### Four spec packages &nbsp;·&nbsp; roles in any federation

<div class="libs-grid">

<svg class="libs-svg" viewBox="0 0 460 320" aria-hidden="true">
  <defs>
    <linearGradient id="libchain" x1="0" y1="0" x2="0" y2="1">
      <stop offset="0%" stop-color="#3a59c8"/>
      <stop offset="100%" stop-color="#6a83e0"/>
    </linearGradient>
    <marker id="libarrow" markerWidth="9" markerHeight="9" refX="8" refY="4.5" orient="auto-start-reverse" markerUnits="userSpaceOnUse">
      <path d="M 0 0 L 8 4.5 L 0 9 z" fill="#3a59c8"/>
    </marker>
  </defs>
  <g class="edges" fill="none" stroke="url(#libchain)" stroke-width="2">
    <line x1="92"  y1="108" x2="92"  y2="206" marker-end="url(#libarrow)"/>
    <line x1="230" y1="108" x2="230" y2="206" marker-end="url(#libarrow)"/>
    <line x1="368" y1="108" x2="368" y2="206" marker-end="url(#libarrow)"/>
  </g>
  <g class="edge-labels">
    <text x="100" y="158" class="dep">uses</text>
    <text x="238" y="158" class="dep">uses</text>
    <text x="376" y="158" class="dep">uses</text>
  </g>
  <g class="lib-box">
    <rect x="25"  y="30" width="135" height="78" rx="10"/>
    <text x="92"  y="60" text-anchor="middle" class="name">@oidfed/authority</text>
    <text x="92"  y="82" text-anchor="middle" class="sub">TAs · Intermediates</text>
  </g>
  <g class="lib-box">
    <rect x="163" y="30" width="135" height="78" rx="10"/>
    <text x="230" y="60" text-anchor="middle" class="name">@oidfed/leaf</text>
    <text x="230" y="82" text-anchor="middle" class="sub">OPs · RPs · RSs</text>
  </g>
  <g class="lib-box">
    <rect x="301" y="30" width="135" height="78" rx="10"/>
    <text x="368" y="60" text-anchor="middle" class="name">@oidfed/oidc</text>
    <text x="368" y="82" text-anchor="middle" class="sub">RP/OP reg flows</text>
  </g>
  <g class="lib-box core">
    <rect x="25" y="216" width="411" height="80" rx="10"/>
    <text x="230" y="248" text-anchor="middle" class="name">@oidfed/core</text>
    <text x="230" y="270" text-anchor="middle" class="sub">primitives · trust-chain · schemas · constants</text>
  </g>
</svg>

<div class="libs-list">

<div class="lib-row">
  <span class="pkg">@oidfed/core</span>
  <p class="role">Foundation. Entity statements, trust-chain resolution, crypto, schemas, constants — every other package depends on it.</p>
  <p class="api"><code>resolveTrustChainForAnchor</code> &nbsp;·&nbsp; <code>generateSigningKey</code> &nbsp;·&nbsp; <code>entityId(url)</code></p>
</div>

<div class="lib-row">
  <span class="pkg">@oidfed/authority</span>
  <p class="role">Runs Trust Anchors and Intermediates. All federation endpoints, subordinate management, key lifecycle, trust-mark issuance &amp; status.</p>
  <p class="api"><code>createAuthorityServer({…}).handler()</code></p>
</div>

<div class="lib-row">
  <span class="pkg">@oidfed/leaf</span>
  <p class="role">Any entity at the edge — OPs, RPs, RSs. Serves <code>/.well-known/openid-federation</code>, discovers and validates peers.</p>
  <p class="api"><code>createLeafEntity({…}).handler()</code> &nbsp;·&nbsp; <code>discoverEntity(id, anchors)</code></p>
</div>

<div class="lib-row">
  <span class="pkg">@oidfed/oidc</span>
  <p class="role">OIDC / OAuth 2.0 profile of federation. RP- and OP-side automatic &amp; explicit registration, Request Object validation.</p>
  <p class="api"><code>automaticRegistration</code> &nbsp;·&nbsp; <code>processAutomaticRegistration</code> &nbsp;·&nbsp; <code>explicitRegistration</code> &nbsp;·&nbsp; <code>processExplicitRegistration</code></p>
</div>

</div>

</div>

---

## The demo federation
### Single-anchor topology &nbsp;·&nbsp; what we will exercise live

<div class="topo-grid">

<svg class="topo-svg" viewBox="0 0 460 310" aria-hidden="true">
  <defs>
    <linearGradient id="chain" x1="0" y1="0" x2="0" y2="1">
      <stop offset="0%" stop-color="#3a59c8"/>
      <stop offset="100%" stop-color="#6a83e0"/>
    </linearGradient>
  </defs>
  <g class="edges" fill="none" stroke="url(#chain)" stroke-width="1.5">
    <path d="M 230 90 Q 230 140 90 220"/>
    <path d="M 230 90 Q 220 140 230 220"/>
    <path d="M 230 90 Q 230 140 370 220"/>
  </g>
  <g class="ta">
    <text x="230" y="14" text-anchor="middle" class="sub">ta.single.fed.oidfed.com</text>
    <text x="230" y="32" text-anchor="middle">Trust Anchor</text>
    <circle cx="230" cy="62" r="22"/>
    <circle cx="230" cy="62" r="10" class="inner"/>
  </g>
  <g class="leaf">
    <circle cx="90" cy="232" r="16"/>
    <circle cx="90" cy="232" r="7" class="inner"/>
    <text x="90" y="264" text-anchor="middle">OP</text>
    <text x="90" y="282" text-anchor="middle" class="sub">op.single.fed…</text>
  </g>
  <g class="leaf">
    <circle cx="230" cy="232" r="16"/>
    <circle cx="230" cy="232" r="7" class="inner"/>
    <text x="230" y="264" text-anchor="middle">RP1</text>
    <text x="230" y="282" text-anchor="middle" class="sub">rp1.single.fed…</text>
  </g>
  <g class="leaf">
    <circle cx="370" cy="232" r="16"/>
    <circle cx="370" cy="232" r="7" class="inner"/>
    <text x="370" y="264" text-anchor="middle">RP2</text>
    <text x="370" y="282" text-anchor="middle" class="sub">rp2.single.fed…</text>
  </g>
</svg>

<div class="topo-list">

<div class="topo-row">
  <span class="role">Trust Anchor</span>
  <span class="url">ta.single.fed.oidfed.com</span>
  <span class="note">runs federation endpoints (fetch, list, resolve, trust-mark)</span>
</div>

<div class="topo-row">
  <span class="role">OP (leaf)</span>
  <span class="url">op.single.fed.oidfed.com</span>
  <span class="note">supports <strong>automatic</strong> &amp; <strong>explicit</strong> client registration · ES256 · scopes <code>openid profile email</code></span>
</div>

<div class="topo-row">
  <span class="role">RP1 (leaf)</span>
  <span class="url">rp1.single.fed.oidfed.com</span>
  <span class="note"><code>client_registration_types: ["automatic"]</code></span>
</div>

<div class="topo-row">
  <span class="role">RP2 (leaf)</span>
  <span class="url">rp2.single.fed.oidfed.com</span>
  <span class="note"><code>client_registration_types: ["explicit"]</code></span>
</div>

</div>

</div>

<p class="topo-foot">Source: <a href="https://github.com/Dahkenangnon/fed-oidfed-com/blob/main/src/topologies/single-anchor.ts">github.com/Dahkenangnon/fed-oidfed-com/blob/main/src/topologies/single-anchor.ts</a></p>

---

## Live demo

<div class="demo-grid">

<div class="demo-main">

<ol class="demo-list">
  <li><code>rp1.single.fed.oidfed.com</code> — <strong>automatic</strong> client registration</li>
  <li><code>rp2.single.fed.oidfed.com</code> — <strong>explicit</strong> client registration</li>
  <li><code>explore.oidfed.com</code> — the same federation, visualised</li>
</ol>

<pre class="ex"># Inspect any participant straight from the terminal — no install needed:
$ npx -y -p @oidfed/cli oidfed entity https://op.single.fed.oidfed.com</pre>

</div>

<aside class="demo-side">
  <span class="eyebrow">Also in the demo fed</span>
  <h3>5 more topologies</h3>
  <ul class="list">
    <li>hierarchical</li>
    <li>multi-anchor</li>
    <li>cross-federation</li>
    <li>constrained</li>
    <li>policy-operators</li>
  </ul>
  <p class="note">The <code>single</code> topology is what we demo today — the other five exercise different corners of the spec on the same <code>@oidfed/*</code> code. Explore any of them via the same CLI or <code>explore.oidfed.com</code>.</p>
  <p class="cta">→ <a href="https://fed.oidfed.com">fed.oidfed.com</a></p>
</aside>

</div>

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
<div class="row">A three-statement chain (Leaf + Intermediate + TA) is already 8–12&nbsp;KB — past nginx's default <code>proxy_buffer_size</code> of 8&nbsp;KB. The 502 fired on the <strong>smallest real federation</strong>, not on a deep edge case.<br><br>The spec already names the carriers that handle this in §12.1.1.1.1 — <em>"… it may be necessary to use the HTTP POST method, a <code>request_uri</code>, or a Pushed Authorization Request [RFC 9126] for the request."</em> In our deployment, that note turned out to describe the default path, not an edge case.</div>

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
<div class="row">§6's extension points (operators, constraints) are designed to be distributed as code and stay static. They aren't shaped to carry decisions whose inputs change <strong>between requests</strong> — entity-graph state, federation-operational mode, dynamic naming.</div>

<span class="lbl">Description</span>
<div class="row">Real cases we ran into: delegation budgets that should narrow during incidents, federation-wide capacity limits, entity-reliability numbers over sliding windows.<br><br>§6.1.1 <em>Determinism</em> rightly keeps these out of static metadata-policy — so this kind of decision can't live inside <code>metadata_policy</code>. Federations that need it today have to build it outside the chain, and lose the federation's signature on the result.</div>

</div>

---

## Feedback&nbsp;3 &nbsp;·&nbsp; runtime policy
### When inputs change between requests &nbsp;·&nbsp; Direction

<div class="fbd">

<span class="lbl lbl-proposal">Direction</span>
<div class="row">What we have in mind is an opt-in <code>federation_policy_evaluation_endpoint</code> that sits next to the static §6 layer — it never replaces it. The endpoint returns a signed decision with <code>iat</code>/<code>exp</code>, so cached decisions stay deterministic inside the window.<br><br>The decision can only narrow what the static layer already allows; it can never loosen it — §6.1.1 Hierarchy preserved.</div>

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
  <span class="name">Yamonwan Justin Dah-kenangnon</span>
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
