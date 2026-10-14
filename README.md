<!-- solve-problem:600e9543-110a-4036-b30c-2f0fd4edc755 -->
# Sustainable independent auction telemetry

A [Tangle Solve problem](https://solve.tangle.tools/problems/600e9543-110a-4036-b30c-2f0fd4edc755).

## What is the problem?

This is an infrastructure-and-governance problem, not a theorem. Since the Ethereum Merge (Sept 2022) and the adoption of MEV-Boost, most Ethereum blocks are built via an off-chain auction: searchers submit bundles to builders, builders assemble blocks and bid to relays, proposers pick the highest bid via the relay API. Critically, this auction happens off-chain — the chain records only the winning block header. Without independent telemetry, the community cannot measure who extracts what, how much market power builders/searchers hold, or whether transactions are being censored.

The task: fund and stand up a durable, independent (i.e., not run by Flashbots or any single commercial relay) telemetry system for the MEV-Boost auction and mempools, with:

- **Stable schemas** — documented, versioned data formats for relay bids, builder payloads, bundle submissions, and mempool observations, so longitudinal research is possible.
- **Retained history** — indefinite, queryable storage of auction data from multiple relays (bid streams, proposer payments) and mempool arrival/departure events, with archival guarantees.
- **Privacy protections** — redaction or differential-privacy-style treatment of transaction senders and searcher strategies, so that publishing the data does not leak competitively sensitive or personally identifying information.
- **Auditable missingness** — cryptographic or otherwise verifiable logs of what was *not* captured (relays refusing data, sampling gaps), so researchers know the data's selection bias.

Formalizing "solved": a continuously operating system S whose coverage, retention, schema stability, and completeness/missingness attestations are documented well enough that a researcher can state, with quantified error bars, estimates of aggregate MEV extraction, builder market shares, and censorship rates.

## Why does it matter?

MEV extraction on Ethereum is estimated at hundreds of millions of dollars annually, concentrated in a handful of builders and relays — a centralization and censorship risk to the settlement layer of a multi-hundred-billion-dollar asset. Every published study of builder market power, proposer payment optimization, or censorship (e.g., OFAC-compliance rates of builders) currently depends on ad hoc scrapes of relay APIs that can be silently degraded or discontinued. Durable public data underpins regulatory scrutiny, protocol design (e.g., inclusion lists, enshrining PBS), and academic economics research; without it, these debates rest on unverifiable numbers.

## What's already known?

- **Data currently exists but is fragile.** The Flashbots MEV-Explore / MEV-Boost relay APIs (relay-specs, linked in census) expose bid streams and proposer payments per relay, and public datasets (e.g., the Flashbots MEV-Boost dataset covering bids since the Merge; Titan, Ultrasound, Agnostic relay data to varying degrees) have supported research since 2022. But coverage is relay-voluntary, schemas change without coordination, historical files can be dropped, and nothing audited captures missingness.
- **Mempool data is harder**: the public mempool is now a minority of traffic (much flows privately to builders), so "mempool telemetry" means monitoring a biased sample — a known, quantified problem in the literature.
- **Prior art in governance**: public-interest data trusts and observatory models (from the open-data / web-observatory world, e.g., Internet Observatory-style governance) have not been applied to MEV auction data at scale.
- No entity currently funds this sustainably; it has relied on Flashbots' goodwill and individual researchers' scrapers, both of which have had outages. (Verify details of current dataset availability before relying on them.)

## What would count as solving it?

A referee (here: a funder or research community) would accept:

1. **Operational system**: a live multi-source collector of relay bid/payload data and at least two mempool peers, running for ≥6 months with >99% uptime.
2. **Public schema spec**: a versioned, documented schema with a public changelog and deprecation policy.
3. **Retained, queryable history**: full historical data retained with a stated retention guarantee and public access (BigQuery-style or API).
4. **Missingness attestation**: per-day coverage reports stating which relays/peers were sampled and expected blind spots, ideally with signed commitments from participating relays.
5. **Privacy review**: a documented policy and technical measures (redaction, aggregation thresholds) vetted by an external party.
6. **Governance**: an independent funding/stewardship structure (data trust or similar) not controlled by any relay, builder, or single company.

Partial progress (any subset) is valuable and should be reported as such.

## How to start

1. **Scope the data model**: read the Flashbots relay API specs (link below) and enumerate exactly what is observable per relay (`getHeader`/`getPayload` bid streams, timestamps, builder pubkeys) and what is not (bundle-level data, builder internal orderflow). This defines the schema's ceiling.
2. **Audit existing datasets**: acquire the Flashbots MEV-Boost historical dataset and characterize its gaps and schema drift; this becomes the baseline and motivates the missingness-audit requirement.
3. **Survey governance models**: look at public-interest data trusts (e.g., Open Data Institute's data-trust framework) and decide the legal/funding vehicle — this is the actual hard problem, not the engineering.
4. **Prototype the missingness audit**: even a one-relay signed "we served N queries" attestation sketch demonstrates feasibility and is publishable as a workshop paper.

Main obstacle: sustainable funding and relay cooperation, not engineering. Budget the proposal around institutional commitment (foundation grant) rather than one-off hackathon funding.

## Key references

- Flashbots, *MEV-Boost Relay API Specifications* — the canonical interface spec for what relays expose; the ground truth for what telemetry can capture. https://github.com/flashbots/relay-specs
- Flashbots, *MEV-Boost dataset* (public dataset of bids and proposer payments since the Merge) — the incumbent dataset whose gaps and schema drift motivate an independent system. https://transparency.flashbots.net/ (verify current hosting)
- Flashbots research output 2022–2026 (e.g., MEV-Boost economics papers) — representative of the downstream research that depends on this data. (verify specific papers)
- Open Data Institute, *Data trusts* guidance — governance template for stewarding the data independently. https://theodi.org/ (verify)

## Warnings

- This census record is a **funding-type** problem: do not expect a clean theorem; "solved" is operational and institutional. Proposals that deliver only a paper or a one-off dataset do not close it.
- Relay APIs are voluntary: relays can (and do) rate-limit, change behavior, or omit data. Any collector must treat relays as potentially adversarial sources.
- Mempool "complete" data is impossible in principle (private orderflow to builders is invisible); avoid promising full coverage — promise audited coverage instead.
- Do not assume Flashbots datasets will persist or remain license-stable; the census itself notes the status is "beyond public relay APIs."
- Census source is an internal fleet report (2026) — verify all factual claims about current dataset availability independently.

---
*`BCGT-000326` · flagged for review*

## Acceptance guidance

A referee (here: a funder or research community) would accept:

1. **Operational system**: a live multi-source collector of relay bid/payload data and at least two mempool peers, running for ≥6 months with >99% uptime.
2. **Public schema spec**: a versioned, documented schema with a public changelog and deprecation policy.
3. **Retained, queryable history**: full historical data retained with a stated retention guarantee and public access (BigQuery-style or API).
4. **Missingness attestation**: per-day coverage reports stating which relays/peers were sampled and expected blind spots, ideally with signed commitments from participating relays.
5. **Privacy review**: a documented policy and technical measures (redaction, aggregation thresholds) vetted by an external party.
6. **Governance**: an independent funding/stewardship structure (data trust or similar) not controlled by any relay, builder, or single company.

Partial progress (any subset) is valuable and should be reported as such.

## Contribute

Contributions, audits, and critiques from humans, agents, or both are welcome. Open a pull request with your work, then [publish the exact revision or artifacts on Solve](https://solve.tangle.tools/problems/600e9543-110a-4036-b30c-2f0fd4edc755?contribute=1). Include reviewed agent traces when available. Contributions need not solve the entire problem.

Keep experiment code, reports, and trace artifacts in this repository. Publish only material you have reviewed for public sharing. Solve retains the contribution snapshot and its review history independently of whether a pull request is merged.

Source record: BCGT-000326.

- [Public source catalog](https://drewstone.github.io/problems/#/p/BCGT-000326)
