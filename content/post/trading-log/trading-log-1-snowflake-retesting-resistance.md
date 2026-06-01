---
title: "Trading Log 1: Snowflake Retesting Resistance, Databricks in the Wings — Where Does That Leave the Data Platform Trade?"
slug: "trading-log-1-snowflake-retesting-resistance"
thumbnailImagePosition: "left"
thumbnailImage: https://logos-world.net/wp-content/uploads/2022/11/Snowflake-Logo.png
coverImage: https://images.unsplash.com/photo-1640161704729-cbe966a08476?w=1600
metaAlignment: center
coverMeta: out
date: 2026-06-01
categories:
- trading-log
tags:
- human-written
- snowflake
- databricks
- investing
- platform
---

Snowflake is retesting its 52-week high, still well below its 2021 all-time high. I am long, I called the earnings surge correctly, and I am now asking the question every winning trade forces: when do I leave?

<!--more-->

I have skin in the game, which biases me toward holding. The point of writing this down is to argue against that bias. I use Databricks daily inside a large bank, so the practitioner view and the investment view both matter to me; I'll try to keep them honest about each other.

To set expectations: this is not a "sell tomorrow" piece. I am structurally cautious on Snowflake at this multiple, but near-term the SaaS sector rotation has further to run. A 52-week-high retest on coordinated cohort flow, with no known binary catalyst inside the next quarter, more often resolves up than down. The structural thesis below is medium-term; the position I am holding is tactical. Don't file the post as "bearish."

## The question the market is actually answering

The lazy framing for the Snowflake–Databricks debate is "warehouse versus lakehouse, winner takes AI." That framing is two years out of date. Snowflake reads and writes Apache Iceberg now, runs Polaris as an open catalog, hosts dbt as its single largest deployment target, and ships LLM functions directly inside SQL. Databricks is converging Delta and Iceberg into a single open format, owns Unity Catalog as a governance plane that increasingly sits on top of storage it does not control, and has built a credible AI platform on top of Mosaic. Both companies are walking toward the same product shape: open storage, governance-led, AI-gateway. The narrative of one eating the other is no longer the right narrative.

What the market is actually pricing is which of the four traditional data-platform moats survive, who owns the moat that replaces them, and whether the duopoly's valuations make sense once you account for the architectural ceilings each company faces. That's a more nuanced question than "AI re-rating."

## Four moats

Every data platform business sits on some combination of four moats: storage lock-in, compute differentiation, governance, and — newly — AI artifact lifecycle ownership. The next three to five years will tell us which survive. My current read:

| Moat | Status | Winner | Confidence |
|---|---|---|---|
| Storage format | Voluntarily commoditized | Neither — both moving to Iceberg | High |
| Compute | Contested, narrowing | Tie for BI; Databricks for ML/ETL | Medium-high |
| Governance | Survives but narrows | Both, at expense of standalone vendors | High |
| AI artifact lifecycle | Emerging | Databricks, meaningfully ahead | Medium |

### Storage is no longer a moat — they killed it themselves

The single most important strategic move of the past 18 months is that both companies have voluntarily disarmed on storage. Snowflake's native FDN format used to be a genuine lock-in mechanism — your data sat in a proprietary container, queryable only by Snowflake compute. That is no longer the dominant pattern. Snowflake Iceberg Tables are first-class citizens; Polaris exposes the catalog openly; and Snowflake's own messaging now emphasizes "your data, your storage." Databricks has moved similarly with Delta-Iceberg UniForm and the publicly announced format convergence.

This is not strategic generosity. It is defensive. Iceberg won the catalog war once AWS, Snowflake, GCP, Confluent, Netflix, and Apple all backed it. Resisting was no longer viable. But the consequence is that *storage is no longer where either company makes its margin*. The data sits in S3 or ADLS in an open format. Either platform can read it. So can DuckDB, Trino, StarRocks, ClickHouse, and Athena. Storage as a moat is dead, and both companies cut the cord themselves.

### Compute is contested, and the gap is narrowing

Snowflake's proprietary vectorized engine — descended from the same academic lineage as Vectorwise and HyPer — was meaningfully faster than Spark SQL for short, concurrent, selective queries throughout the late 2010s and early 2020s. That gap is materially smaller today. Databricks Photon closes most of the BI gap; Spark wins decisively on ML and large-scale ETL; and a long tail of Iceberg-native engines (StarRocks for OLAP, DuckDB for single-node analytics, ClickHouse for time series) put real pressure on the floor.

What Snowflake still does better: virtual warehouse isolation at scale, concurrent BI workloads with hundreds of analysts hitting dashboards, and zero-touch operations. The "no Spark configs to tune" experience is a real product. Whether it justifies the multiple premium is a separate question.

What Databricks does better: anything that needs Python, anything that needs GPUs, anything that needs MLflow, and anything where you want to bring your own cloud account. Inside my bank, we use Databricks for the things that would be operationally miserable on Snowflake — feature pipelines, model training, large historical reprocessings — and Databricks dashboarding is fine for the non-real-time BI we run on top of it. Real-time dashboards are the one place we still reach elsewhere.

### Governance survives, but as a feature, not a product

This is where my practitioner view diverges from the standard analyst take. The conventional wisdom is that governance — catalogs, lineage, column-level ACLs, audit logs, PII tagging — gets *more* important in an agentic world. I agree. If LLM agents are writing four hundred queries an hour, the governance plane is the only control surface left. That's not in dispute.

What *is* changing is the cost to build a governance plane. Historically, products like Unity Catalog, Snowflake Horizon, Collibra, Alation, and Atlan commanded premium prices because the work was enormous: catalog plus lineage plus policy engine plus a UI plus integrations with fifty BI tools, twenty orchestrators, ten query engines, each with its own auth and schema-extraction quirks. The open-source community never built this because the payoff — enterprise sales — didn't match OSS contributor incentives. Nobody writes a Looker lineage parser for kudos.

AI coding collapses that asymmetry. A competent platform team at a mid-sized enterprise can now plausibly build a "good enough" governance plane in months instead of years: an Iceberg catalog, OpenLineage emitters, an OPA-based policy engine, a React UI, and a handful of well-chosen connectors. The agentic-friendly turn in SaaS means those connectors are also cheaper to build and maintain than they were two years ago.

What this means for the moat: governance does *not* die as a category. It dies as a *standalone product category*. The Fortune 500 will keep buying Unity Catalog or Horizon because they are bundled with compute the customer already needs. The next five thousand enterprises — the ones reluctantly paying Atlan or Collibra because building was previously impossible — can plausibly build internally. That's TAM compression for the pure-plays, and it pushes governance value into the compute-plus-governance bundles owned by Databricks and Snowflake. Both companies win the consolidation. Neither captures premium pricing on governance as a line item.

### AI artifact lifecycle — the moat that actually matters

The newest moat, and the one where the platforms diverge most cleanly. AI artifact governance — who owns the model, the eval data, the lineage from training to serving, the prompt registry, the agent runtime — is the next prize, and Databricks is meaningfully ahead.

This is downstream of GPU access, not GPU resale margin. Databricks resells GPU capacity across AWS, Azure, and GCP, which is a low-margin commodity business. That's not why GPU access matters. It matters because *the customer workload that owns the model lifecycle lives wherever the GPUs are*. Once a team trains, fine-tunes, and serves on Databricks, the model artifacts and the governance graph sit in Unity Catalog. The AI control plane belongs to Databricks because of where the work happened, not because of any specific feature.

Snowflake's response — Cortex Complete, Cortex Analyst, Cortex Search, Snowflake Container Services — is real, and for the dominant enterprise pattern of "RAG against governed structured data" it is good enough. CFOs ship Cortex Analyst on time. But Snowflake is not where models are trained, fine-tuned, or served at scale. That work happens on Databricks, on the hyperscalers' native AI stacks, or on NVIDIA's (DGX + NIM + AI Enterprise). Snowflake's AI surface is bounded by what fits inside a warehouse query.

## What I see in the field, and what it implies

From what I see in large regulated enterprises, the most sensitive workloads — PII, regulated customer data, anything that touches a supervisory examination — run on-prem. Getting Snowflake or Databricks approved for those workloads at all is a fight with internal risk and the regulator's expectations, and the answer is often "no, or only under conditions that make the platform useless for the workload." That's why these platforms tend to sit in the "innovation budget" line: they get adopted for the workloads that don't touch the sensitive data, and the relationship is good there. The workloads they can't touch are large, and they are exactly the ones the on-prem and sovereign-AI vendors are positioning for.

The pattern is consistent across regulated industries. Data platforms that don't run inside an on-prem or sovereign-cloud boundary do not get the regulated production workloads, no matter how good their cloud SaaS story is. That's a smaller market than either Snowflake or Databricks is currently valued for, and it's the structural shape of regulated enterprise procurement today, not a future risk.

The Databricks BYOC model — running Databricks inside the customer's own cloud account — is a stronger response to this than Snowflake's pure-SaaS posture. Customers see the underlying EC2 and GPU bills directly and pay Databricks only a markup on the DBU. That transparency becomes important once the agentic-pricing crisis arrives: when an agent burns through a quarter's data budget in two weeks because it ran reflection loops on every query, the CFO wants to see what the platform charged versus what the cloud charged. Databricks survives that conversation more cleanly than Snowflake does. Snowflake's opaque credits, designed for pre-AI consumption patterns, are structurally vulnerable to bill shock.

The semantic layer — dbt's, Cube's, LookML's, and the platform-native ones — is the agentic interface layer. Whoever owns it owns the agent's query surface. dbt is winning that race today, but I don't think the moat is durable. Semantic layers are not architecturally difficult, and both Snowflake and Databricks are absorbing the feature. It's the GPU-reselling pattern again: a structurally necessary capability with thin defensible margin.

## The three-segment compression

The bear case is usually told one segment at a time: hyperscalers eat the mid-market, regulated is out of reach, OSS picks off the long tail. Each thread alone sounds resistible. The market misses that all three happen at once.

The strong version — "they lose all three segments" — overshoots. SMEs won't run their own OSS stack (no one to be on-call). Mid-market won't migrate the day AWS ships S3 Tables (hyperscalers ship primitives, only Microsoft is packaging them into a product). Large regulated won't replace data platforms with internal builds (the binding constraint is risk-committee bandwidth, not engineering). The right version is softer: *margin pressure across all three segments at once, without any one segment getting lost outright*.

| Segment         | What changes                                                                                                                       | Who captures the released value                                                                      |
| --------------- | ---------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------- |
| SME             | SaaS bundled with the customer's existing cloud/Office relationship displaces standalone data-platform contracts                   | Microsoft Fabric, Google BigQuery, AWS native bundles — not OSS                                      |
| Mid-market      | Hyperscaler primitives (S3 Tables, OneLake, BigLake) commoditize the warehouse layer as they get packaged into end-to-end products | Microsoft (via Fabric) is furthest along on product packaging; AWS and GCP are still primitive-heavy |
| Large regulated | On-prem and air-gapped AI segment captured by vendors with on-prem capability and compliance certifications, or by internal builds where the regulator effectively requires it | NVIDIA, Palantir, IBM watsonx in much of regulated; internal builds in at least some banks; possibly Databricks if they ship Anywhere |

Regulated is already squeezing; SME and mid-market play out over years. Revenue keeps growing in absolute terms — that's not in dispute. The question is whether Snowflake and Databricks grow as platform-monopoly compounders (what the valuation assumes) or as critical infrastructure under margin pressure (my view). That gap is the entire trade.

Large regulated is where the two platforms diverge most sharply, so it's worth its own section.

## The structural asymmetry inside large regulated: on-prem

Snowflake architecturally cannot run on-prem. Its separation of storage and compute depends on cloud object-storage semantics — durability, scalability, consistency, pricing — that on-prem object stores can't fully replicate. The micro-partition machinery, the FDN format, the metadata service all assume cloud object storage. Snowflake leadership has effectively said on-prem is not on the roadmap. This isn't a strategic choice; it's an architectural prison.

Databricks *can* run on-prem. The runtime is Spark on Kubernetes; that runs anywhere. They already ship Databricks on Google Distributed Cloud for sovereign deployments — one step from a full on-prem product. I don't know why they haven't shipped one; my guess is some mix of slower release cadence, heavier services layer, and different engineering culture than pure SaaS. That's outside-in speculation, not anything from inside Databricks.

| Dimension | Snowflake | Databricks |
|---|---|---|
| Can technically run on-prem | No | Yes |
| Has shipped on-prem | No | No |
| Why not | Architectural prison | Margin and culture choice |
| Addressable regulated/air-gapped AI TAM | Locked out | Optional |

The regulated and air-gapped AI TAM — banks, defense, healthcare, sovereign government, EU under DORA — is large and growing, and neither Snowflake nor Databricks is addressing it. (Mainland China is excluded; CN has its own ecosystem and Western vendors won't reach it regardless.) Defense, healthcare, and sovereign spend goes to Palantir, IBM watsonx, Cloudera's long tail, and NVIDIA's on-prem AI stack. In banks the pattern is less uniform — in my own bank the most regulated workloads are built internally, slowly but eventually, and I'd guess some other banks look the same, though I wouldn't generalize. Either way, the spend doesn't go to Snowflake or Databricks.

This is the cleanest divergence between the two platforms. Snowflake's ceiling is architectural; it can't follow this TAM regardless of strategy. Databricks' ceiling is policy choice. My base case: Databricks ships some form of "Databricks Anywhere" within ~24 months as a defensive response to NVIDIA, two years late, capturing the long tail. Snowflake will issue a press release about a read-only Iceberg-on-prem partnership with Pure Storage and pretend that counts.

The market currently prices Snowflake at a premium to Databricks' implied private valuation. Under this asymmetry, that is backwards.

## The trade versus the thesis

The tactical trade and the structural thesis point in different directions on different timeframes.

Near-term I lean cautiously bullish. Snowflake is retesting its 52-week high, has already failed it once today, and won't resolve until later today or tomorrow at earliest. The context is supportive — SaaS cohort rotation, leaders making fresh highs, no Databricks S-1 inside the next quarter — and 52-week retests in that environment tend to resolve up more than down. If it breaks, overhead supply from 2021 makes the path choppy but the move can run multiple quarters. If it rejects, the structural re-rating starts now.

The **structural thesis**: Snowflake is priced for the AI-platform re-rating story, but the business is converging toward a governance-bundled warehouse with a TAM ceiling, real AI-workload competition from Databricks, and no path into the on-prem regulated-AI TAM. Fair value is materially below the current price.

The **tactical trade**: a 52-week-high retest with the cohort bid, no Databricks S-1, no near-term catalyst. Either the level breaks and flow carries it higher, or it rejects and the re-rating begins.

Both can be true at once. The discipline is not letting the tactical trade rewrite the thesis.

### Valuation: what is Snowflake worth?

The Datadog comparison is the most useful frame. Datadog is making fresh all-time highs while Snowflake is still well below its 2021 peak, yet they sit at roughly the same market cap and a broadly similar forward-revenue multiple. People use that to argue Snowflake has room to catch up. I think it argues the opposite.

| | Datadog | Snowflake |
|---|---|---|
| Competitive structure | Effective duopoly | Three-way fight with structural ceiling |
| Architectural ceiling | None | On-prem locked out |
| Major competitor about to IPO | No | Yes (Databricks) |
| Net revenue retention | Stable | Declining |
| Pricing model risk | Low | High (agentic consumption shock unsolved) |
| AI narrative dependence | Moderate | High |

Datadog is the cleaner business. At parity, the market is already giving Snowflake credit it hasn't earned. Datadog reached that valuation through years of consistent execution; Snowflake is being asked to grow into it. Parity is a ceiling, not a floor.

I don't want to fake precision on fair value — the directional read is enough. If the AI-platform story is right, premium to Datadog. If my thesis is right, discount to Datadog. If hyperscaler squeeze and agentic-pricing crisis both arrive, closer to mature software peers. I weight the middle case heaviest, the bullish case as overpriced by current narrative, and the bearish case as underpriced tail risk. Fair value sits below the current price. The position pays off only if the tactical move runs further before the re-rating arrives — a bet on timing, not on fundamentals.

## What would change my mind

The thesis is falsifiable, which is the only kind worth writing down.

- **Cortex disclosed at greater than a quarter of Snowflake's total revenue within six quarters.** Cortex would then be a platform, not a feature, and the AI re-rating story would be structurally correct.
- **Snowflake announces a credible on-prem product** — not a Pure Storage partnership press release, but deployable software with object-storage abstraction. Cracks the architectural ceiling.
- **Databricks IPOs at a meaningful discount to its last private mark.** The platform-monopoly story would have collapsed earlier than my thesis assumes, making Snowflake's relative position stronger by symmetry.
- **Microsoft Fabric attach rate stalls or reverses.** The hyperscaler squeeze is the single largest risk to both companies; its absence would expand the duopoly's runway.
- **A neutral, Iceberg-native AI control plane emerges** — from NVIDIA, a hyperscaler-neutral player, or open source — and gains enterprise adoption. Structural break for both companies in opposite directions from how the market currently models them.

## The meta point

The market keeps pricing data platforms on their *last* moat — first storage, then compute, now AI. The next one is the control plane: governance, AI runtime, semantic layer, supply security, deployment topology. Both companies are walking toward it. Neither will own it cleanly.

My base case: Snowflake and Databricks become utilities, not platforms. Revenue keeps growing, share of the conversation keeps shrinking, and margin gets compressed across every segment at once. That's a fine business. It is not the business their valuations describe.

I am long Snowflake into the resistance test, and watching the Databricks S-1 as the signal the structural re-rating has started.

---

*A note: I'm writing this with Claude as a thinking partner, while eagerly watching SNOW take another run at the 52-week-high resistance.*
