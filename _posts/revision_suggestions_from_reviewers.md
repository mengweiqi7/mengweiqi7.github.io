---
title: 'Blog Post number 6'
date: 2026-07-12
permalink: /posts/2026/07/blog-post-6/
tags:
  - cool posts
  - category1
  - category2
---

# Detailed Revision Suggestions Based on Reviewer Comments

Manuscript: **Day-Ahead Offering for Virtual Power Plants: A Stochastic Linear Programming Reformulation and Projected Subgradient Method**

Purpose of this document: give an acceptance-oriented revision strategy for the current manuscript version, using the reviewers' comments and the submitted PDF. The strategy prioritizes revisions that answer all reviewer concerns while avoiding major re-modeling, re-formulation, and re-simulation wherever possible.

Important caveat: no revision can guarantee acceptance, especially after an outright rejection. The goal is to remove the main reasons for rejection and prevent the same objections from recurring.

## 1. Core Revision Strategy

The most efficient path is **not** to broaden the model. Broadening the VPP to include conventional units, network constraints, multi-stage real-time uncertainty, negative prices, and market power would destroy the structure that makes the reformulation and algorithm work. Instead, revise the paper around a narrower but more defensible claim:

> This paper studies a clearly defined class of price-taking, single-settlement-node, PV-storage-load VPP offering problems with convex real-time operation and a stylized two-settlement imbalance mechanism. For this class, the paper gives an exact structural reduction of the second-stage adaptive-robust recourse problem and an efficient first-order oracle that returns both recourse values and subgradients for a projected subgradient method.

This framing is more likely to satisfy OR reviewers because it turns the paper from "a VPP application with many assumptions" into "an exact algorithm for a well-defined structured stochastic adaptive-robust optimization class."

The revision should therefore do four things:

1. **Narrow the scope explicitly.**
   Do not call the assumptions "mild." Call them "structural assumptions defining the exactness domain."

2. **Strengthen the OR contribution.**
   The novelty is not "we used RO, SP, PSM, and greedy search." The novelty should be stated as:
   - identifying a VPP offering subclass whose robust recourse can be decoupled exactly;
   - converting the recourse into a separable convex piecewise-linear resource allocation problem with nested cumulative constraints;
   - designing an oracle that returns both the optimal recourse value and a valid first-stage subgradient in one pass;
   - embedding this oracle in a projection structure induced by monotone offer curves.

3. **Be honest about exactness.**
   Proposition 2 is exact only under price and penalty assumptions. If those assumptions fail, the decoupling is not exact and the method should not be claimed as exact. This honesty will reduce reviewer resistance.

4. **Separate theory from engineering acceleration.**
   The local search oracle can have a rigorous finite optimality proof. The BB/backtracking PSM-PAVA implementation should be presented as an accelerated practical variant unless a rigorous nonsmooth convergence proof is added. Add a standard projected subgradient convergence statement for a conservative theoretical step-size rule.

## 2. Highest-Priority Changes

These changes should be made before any further numerical work.

### 2.1 Retitle the Paper

Reviewer 2 explicitly notes that the current title does not reflect robust optimization. The title should also avoid sounding like the paper's contribution is only "a reformulation and a method."

Recommended title:

> **A Structure-Exploiting First-Order Oracle for Two-Stage Stochastic Adaptive-Robust Virtual Power Plant Offering**

Alternative:

> **Day-Ahead Offering for Price-Taking Virtual Power Plants: A Two-Stage Stochastic Adaptive-Robust Reformulation and First-Order Oracle**

Why this helps:

- Includes "stochastic adaptive-robust."
- Signals the OR contribution: "structure-exploiting first-order oracle."
- Narrows the market setting: "price-taking VPP."

### 2.2 Rewrite the Abstract

The current abstract overemphasizes CC&G motivation and "mild assumptions." Reviewer 3 specifically questions the CC&G sentence. Remove or sharply clarify that sentence.

Suggested abstract structure:

1. Problem and market setting in one sentence.
2. Modeling scope: price-taking PV-storage-load VPP, stochastic day-ahead price scenarios, robust PV availability.
3. Main theoretical result: under explicitly stated structural conditions, the max-min recourse decouples and reduces to a separable convex piecewise-linear problem with nested cumulative constraints.
4. Algorithmic result: local-search oracle returns optimal recourse value and a subgradient with respect to offer quantities.
5. Outer method: projected subgradient with PAVA projection for monotone offer curves.
6. Computational evidence: speedups versus LP solver and Benders/outer-approximation or CC&G baselines.
7. Scope sentence: exactness is within the stated structural domain.

Suggested replacement wording:

> We study the day-ahead offering problem of a price-taking virtual power plant that aggregates photovoltaic generation, battery storage, and deterministic demand at a single settlement node. Day-ahead prices are represented by stochastic scenarios, while photovoltaic availability is protected by a budgeted uncertainty set. For this structured two-stage stochastic adaptive-robust model, we identify conditions under which the robust second-stage recourse problem decouples into a linear maximization over photovoltaic availability and a separable convex piecewise-linear storage dispatch problem with nested cumulative constraints. We then develop a local-search recourse oracle that computes both the exact second-stage value and a valid subgradient with respect to first-stage offer quantities. Combined with PAVA projection onto monotone offer curves, this oracle yields a scalable projected subgradient algorithm. Numerical experiments using utility data show that the proposed oracle matches LP benchmark solutions while reducing recourse computation time, and that the resulting first-order method substantially reduces total computation time relative to cutting-plane outer-approximation baselines. We also clarify the structural assumptions under which the reformulation is exact and discuss extensions beyond this domain.

This wording is safer because it avoids saying "mild" and avoids suggesting the method solves general VPP offering.

### 2.3 Rewrite the Contribution Section

Current contribution list is vulnerable because each item sounds like a known technique. Rewrite it to emphasize what is tied specifically to the problem structure.

Recommended contribution list:

1. **Modeling scope and exactness domain.**
   Define the VPP offering subclass: price-taking, single settlement node, PV-storage-load aggregation, convex real-time dispatch, terminal storage neutrality, and stylized imbalance penalty. State that these assumptions are not universal; they are the domain where exact structural results hold.

2. **Exact recourse decoupling under checkable conditions.**
   Present Proposition 2 as a checkable exactness result, not as a mild assumption. Emphasize that the worst-case PV profile can be computed independently because the active PV coefficients preserve a price-induced ordering.

3. **First-order recourse oracle for nested resource allocation.**
   The key OR contribution is that the second-stage problem is not merely solved fast; the same local-search pass identifies active PWL pieces and returns a subgradient with respect to first-stage offer quantities. This is the feature not provided by generic network-flow or breakpoint-search methods.

4. **Projected first-order method exploiting isotonic offer structure.**
   The first-stage feasible set has separable monotone offer curves, so projection decomposes into bounded isotonic regressions solved by PAVA.

5. **Computational decomposition of speedup.**
   Report speedup as two components: structural recourse reformulation/oracle speedup and outer first-order versus cut-accumulation speedup. Avoid claiming a universal two-orders-of-magnitude advantage.

Use this sentence:

> The contribution is not the individual use of robust optimization, projected subgradients, or greedy resource allocation, but the identification of a VPP offering structure under which these components form an exact value-and-subgradient oracle for a two-stage stochastic adaptive-robust model.

## 3. New Section To Add: "Modeling Scope and Structural Assumptions"

Add this near the end of Section 2.1 or immediately before the optimization formulation. This is the single most important revision because it answers Reviewers 2, 3, 4, and 5.

Use a table like this:

| Assumption | Meaning | Used in | If relaxed |
|---|---|---|---|
| A1 Price-taking VPP | Offers do not affect day-ahead clearing prices; prices are exogenous scenarios | Scenario model, first-stage objective | Strategic/bilevel market-clearing model needed |
| A2 Single settlement node | DERs are electrically close or settled at the same price node | One price per period, no network constraints | Multi-node model with locational prices and network constraints |
| A3 Aggregate PV-storage-load portfolio | Individual devices are represented by aggregate equivalent capacities | Device model, case study | Unit-level indices can be added if convex and separable; binary units break results |
| A4 Convex real-time operation | No conventional unit commitment, minimum up/down, or binary operating modes | Proposition 1, LP recourse | Mixed-integer recourse; greedy oracle no longer exact |
| A5 Terminal storage neutrality | End-of-day SOC equals initial SOC | Nested cumulative constraints in (28) | Terminal interval or terminal value function needed |
| A6 Positive-price exactness domain | Prices satisfy the conditions in Proposition 2 | PV-boundary result and max-min decoupling | Decoupling may fail; original recourse or MILP fallback needed |
| A7 Static daily PV uncertainty set | Day-ahead offering is protected against a daily PV availability envelope | PV robust set and Proposition 2 | Multi-stage adjustable robust model needed |
| A8 Reduced-form imbalance penalty | Deviations are settled through a stylized penalty spread around the day-ahead price | Cost model and Proposition 1 | Market-specific settlement model needed |

Add a paragraph:

> These assumptions should not be interpreted as a complete model of all VPPs. They define a practically relevant subclass of VPP offering problems for which exact structural simplification is possible. The extension to dispatchable generators, network-constrained aggregations, negative-price regimes, or multi-stage recourse is outside the exactness domain of the present reformulation and is discussed in Section 6.

This directly answers the accusation that the problem was "tailored" without admitting a fatal flaw.

## 4. Specific Responses To Modeling Critiques

### 4.1 VPP Without Conventional Generation

Reviewer concern:

- The VPP excludes conventional generation, making real-time operation convex and time separable.
- If conventional units are added, Proposition 1 is insufficient.

Revision:

- Do not add conventional generation.
- State explicitly that the paper focuses on DER aggregators whose controllable flexibility is storage, not thermal generation.
- Add one paragraph explaining that adding conventional units with commitment decisions introduces binary variables and invalidates the separable convex recourse structure.

Suggested text:

> The VPP considered here represents an aggregation of inverter-based renewable generation, battery storage, and demand behind a common market interface. We intentionally exclude conventional generators with commitment decisions. This exclusion is not a claim that all VPPs have this structure; rather, it defines the convex operating regime in which the recourse problem admits the resource-allocation structure studied in Sections 3 and 4. If dispatchable generators with start-up, minimum-output, or minimum-up/down constraints are included, the second-stage problem becomes mixed-integer and Proposition 1 and the local-search oracle no longer apply directly.

### 4.2 Aggregated Device Inconsistency

Reviewer concern:

- Section 2 says "a solar PV unit, an energy storage unit, and an aggregated load."
- Case study says 200 PV units and 100 storage devices.
- Indices are missing.

Efficient fix without re-modeling:

- Do not add unit-level indices throughout the model.
- Define all variables as aggregate-equivalent portfolio quantities.
- Replace "a solar PV unit, an energy storage unit" with "an aggregate PV portfolio and an aggregate storage fleet."
- Add aggregation equations:

```latex
\bar p^{PV}_t = \sum_{i \in \mathcal I^{PV}} \bar p^{PV}_{i,t}, \qquad
\bar p^{ES} = \sum_{j \in \mathcal I^{ES}} \bar p^{ES}_j, \qquad
\bar e = \sum_{j \in \mathcal I^{ES}} \bar e_j .
```

Then state:

> After aggregation, the model uses portfolio-level variables. The case study's 200 PV units and 100 storage devices are therefore represented by aggregate capacity and energy limits rather than unit-level decision variables.

This solves the inconsistency without reformulating the paper.

### 4.3 Terminal Constraint `e_T = e_0`

Reviewer concern:

- The use of terminal equality is not practically justified.

Revision:

- Keep `e_T = e_0`, but justify it as daily-cycle neutrality and prevention of inter-day energy borrowing.
- State it is common in daily storage scheduling when the next day is not modeled.
- Add a limitation: if inter-day arbitrage is modeled, a terminal value function or terminal SOC interval is preferable.

Suggested text:

> The equality `e_T = e_0` imposes daily energy neutrality. It prevents the model from creating artificial profit by ending the modeled day with depleted storage while ignoring the cost of restoring the battery state for the next operating day. This is appropriate for a rolling daily offering study in which each day is optimized under the same initial operating policy. If multi-day operation is modeled, the equality can be replaced by a terminal value function or an admissible terminal SOC interval, but the nested-constraint structure used by the local-search oracle would need to be modified accordingly.

### 4.4 Positive and High Price Assumptions

Reviewer concern:

- Positive prices and prices higher than PV marginal cost are restrictive.
- Negative prices occur in real markets.
- Proposition 2 depends critically on these assumptions.

Revision:

- Do not hide this. Make it a central exactness condition.
- Replace "mild modeling assumptions" with "structural price conditions."
- Add a table verifying the condition in all numerical experiments. The values should be computed from the existing `price_interval.mat`, scenario files, `c^{PV}`, and `kappa` used in the case study before the manuscript is resubmitted:

| Quantity | Value in experiments | Required by theory | Satisfied? |
|---|---:|---:|---|
| `min_{t,omega} lambda^DA_{t,omega}` | computed from sampled price trajectories | `> max{kappa, c^PV}` | report result |
| `min_{t,omega} (lambda^DA_{t,omega} - c^PV)` | computed from sampled price trajectories | `>= kappa` | report result |
| `kappa` | value used in experiments | positive | report result |
| minimum positive intertemporal price separation | computed within each sampled trajectory | `>= 2 kappa` if strict ordering is used | report result |

Important mathematical correction:

The current condition

```latex
\kappa \le \min_{t \ne t'} |\lambda^{DA}_{t,\omega} - \lambda^{DA}_{t',\omega}|/2
```

is problematic if two periods have equal prices. It would force `kappa = 0`. Revise the condition using one of these safer alternatives:

1. Use only distinct price levels:

```latex
\kappa \le \frac{1}{2}
\min_{\omega,\, t,t':\, \lambda^{DA}_{t,\omega} \ne \lambda^{DA}_{t',\omega}}
|\lambda^{DA}_{t,\omega} - \lambda^{DA}_{t',\omega}|.
```

2. Or state a weak-ordering condition:

```latex
\lambda^{DA}_{t,\omega} > \lambda^{DA}_{t',\omega}
\quad \Rightarrow \quad
\lambda^{DA}_{t,\omega} - \kappa \ge \lambda^{DA}_{t',\omega} + \kappa .
```

This second version is logically clearer because it says exactly what is needed: the possible PV coefficients preserve the price ordering regardless of the active affine piece.

Add a negative-price remark:

> If the price condition is violated, for example during negative-price periods or when the day-ahead price is below the PV marginal cost plus the penalty spread, PV curtailment may become optimal and the boundary result in Proposition 2 need not hold. In that case, the decoupled LP in (35) should be treated as a heuristic approximation unless the original max-min recourse problem is solved directly. The exactness claims and computational comparisons in this paper are therefore restricted to instances satisfying Assumption A6.

This is necessary. It is better to admit a limitation precisely than to let reviewers conclude the theory is wrong.

### 4.5 Price-Taker Assumption

Reviewer concern:

- Price-taking is essential but not discussed.

Revision:

- Add it in Section 2.1, before the price scenario model.

Suggested text:

> We adopt a price-taker setting: the VPP's submitted offer curve determines its accepted quantity at the realized clearing price but does not affect the clearing price itself. This assumption is appropriate for an aggregator whose capacity is small relative to the market-clearing supply stack or whose offer is evaluated against exogenous price scenarios. Modeling price impact would require embedding market clearing or a bilevel equilibrium model and is outside the scope of the present structural reformulation.

This also helps remove irrelevant bilevel citations later.

### 4.6 Single-Node / No Distribution Network

Reviewer concern:

- The VPP ignores distribution network and location-dependent pricing.

Revision:

- Add this to the scope table and market setting.
- Say the VPP is behind one settlement point or electrically proximate enough to use one market price.
- Discuss multi-node/network extension in the conclusion, but do not model it.

Suggested text:

> The aggregation is assumed to be settled at a single pricing node. Thus, the day-ahead offer is a portfolio-level net injection and all DERs face the same settlement price in each period. This assumption is consistent with a VPP operated behind a common market interface. If DERs are geographically dispersed across nodes with different locational prices or binding distribution constraints, the model must be extended to a network-constrained formulation; the present recourse decoupling is not claimed for that setting.

### 4.7 Imbalance Settlement Cost

Reviewer concern:

- The settlement cost seems artificial and not tied to real market rules.

Revision:

- Change the tone. Do not say "to comply with U.S. market rules" as if the exact function is implemented by an ISO.
- Say it is a stylized reduced-form model motivated by two-settlement deviations and uninstructed deviation penalties.
- Explain the purpose: prevent artificial arbitrage between day-ahead and real-time deviations in a price-taking model.
- Add calibration guidance for `kappa`.

Suggested text:

> The penalty spread `kappa` is a reduced-form representation of deviation charges and uninstructed-deviation penalties rather than a literal tariff formula. Its role is to encode the economically standard condition that intentional deviations from day-ahead commitments should not be rewarded. In empirical implementation, `kappa` can be calibrated from observed imbalance price spreads, tariff penalty bands, or sensitivity analysis. The exact structural result in Proposition 2 is conditional on this penalty spread satisfying Assumption A6.

Then in experiments:

- Report the value of `kappa`.
- State how it was selected.
- Add a sensitivity plot or table for `kappa = 0.5 kappa_0`, `kappa_0`, and `1.5 kappa_0` if easy. If not, add a sentence that the value is selected to satisfy the exactness condition and to represent a conservative deviation penalty.

### 4.8 Hybrid Price-Stochastic / PV-Robust Uncertainty

Reviewer concern:

- Why price is stochastic but PV is robust is not sufficiently justified.
- PV uncertainty set calibration is unclear.

Revision:

Add a subsection "Rationale for Hybrid Uncertainty Modeling" after Section 2.3.

Core logic:

- Price uncertainty is market uncertainty. The VPP is risk-neutral over market revenue and historical price data support scenario generation.
- PV availability is physical delivery risk. The VPP must satisfy commitments under renewable forecast error, and probabilistic PV error distribution may be less reliable for tail events.
- The hybrid model does not mean price and PV are statistically coupled. It means the recourse cost couples price scenarios with robust PV realizations operationally.

Suggested text:

> We model price and PV uncertainty differently because they play different operational roles. Day-ahead prices determine market revenue and are represented by empirical scenarios, consistent with a risk-neutral expected-profit objective. PV availability determines physical deliverability of the accepted offer; therefore, we use a budgeted uncertainty set to protect against forecast error within a daily envelope. This hybrid construction is not meant to assert a joint probabilistic model of price and PV. Rather, price scenarios and PV uncertainty interact through the recourse value function: for each price trajectory, the worst-case PV profile is selected and the storage dispatch is optimized.

PV calibration:

Add a short paragraph:

> The bounds `underline p^{PV}_t` and `bar p^{PV}_t` are calibrated from forecast prediction intervals or empirical quantiles of historical forecast errors. The budget `Gamma` is selected by backtesting: choose the smallest value for which the validation-set PV trajectories fall inside the set at the desired coverage level.

Add a small calibration table if data are available:

| Parameter | Meaning | Value | Calibration source |
|---|---|---:|---|
| PV lower bound | lower prediction band | fill | historical PV forecast error |
| PV upper bound | upper prediction band | fill | historical PV forecast error |
| `Gamma` | maximum total normalized deviation | 6, 12 | risk-conservatism settings |

### 4.9 Timing and Nonanticipativity

Reviewer concern:

- Day-ahead prices are revealed simultaneously, so Markov process is not a sequential decision model.
- The statement that nonanticipativity is enforced by `(t,s)` indexing is misleading.
- Real-time operational decisions are sequential, but the paper treats them as two-stage.

Revision:

This is crucial. Do not defend the current language. Replace it.

For day-ahead prices:

- Say the Markov chain is a scenario-generation device for temporal correlation, not a sequential revelation model.
- Say the day-ahead offer curve is submitted before price realization; after market clearing, the accepted quantity for each hour is evaluated at that hour's clearing price state.
- Avoid the word "nonanticipativity" unless defining it carefully.

Suggested replacement:

> The Markov model is used only to generate temporally correlated daily price trajectories. It does not imply that the VPP observes hourly day-ahead prices sequentially or revises the submitted offer during the day-ahead clearing process. The offer curve for every hour is submitted before clearing. Once the day-ahead price vector is realized, the accepted quantity in hour `t` is determined by the price state `s_t(omega)` and equals `p^{DA}_{t,s_t(omega)}`.

Add explicit mapping:

```latex
p^{DA}_{t}(\omega) =
\sum_{s \in S} p^{DA}_{t,s} \mathbf{1}\{s_t(\omega)=s\}.
```

For real-time recourse:

- State the model is not a multi-stage real-time policy.
- It is a daily recourse evaluation after PV availability profile is realized, or a conservative approximation of sequential operation.

Suggested text:

> The second stage represents the operational feasibility and cost of implementing the cleared day-ahead offer under a realized daily PV availability profile. It does not model intra-day information updates as a multi-stage control policy. This abstraction is appropriate when the paper's objective is day-ahead offer construction rather than real-time dispatch policy design. A multi-stage extension with sequential PV forecast updates would be more realistic but would not preserve the present recourse structure.

This directly addresses Reviewer 3 and prevents future rejection on timing ambiguity.

## 5. Problem Formulation Fixes

### 5.1 Equation (13) and Accepted Quantity Mapping

Reviewer concern:

- The if-then condition in (13) is incomplete.

Current issue:

- `p^{DA}_{t,s}` is called both a submitted offer quantity and sometimes "day-ahead price."
- It is not fully clear how scenario `omega` selects a committed quantity.

Revision:

Add a definition before (13):

```latex
q^{DA}_t(\omega) := p^{DA}_{t,s_t(\omega)}
= \sum_{s \in S} p^{DA}_{t,s} \mathbf{1}\{s_t(\omega)=s\}.
```

Then use `q^{DA}_t(\omega)` or `p^{DA}_t(\omega)` consistently as the committed net injection. Do not call it "day-ahead price."

Revise (13):

```latex
\mathcal X =
\left\{
p^{DA} \in \mathbb R^{T \times |S|}:
\underline p^{DA} \le p^{DA}_{t,s} \le \bar p^{DA},
\quad
p^{DA}_{t,s} \le p^{DA}_{t,s'}
\ \text{whenever } \pi_{t,s} \le \pi_{t,s'},
\ \forall t, s, s'
\right\}.
```

Add one sentence:

> This condition enforces monotonicity of the submitted offer curve: higher clearing-price states cannot correspond to lower offered net injection.

### 5.2 Notation Consistency

Fix these throughout:

- Use `lambda^{DA}` only for prices.
- Use `p^{DA}` or `q^{DA}` only for committed offer quantities.
- Use "offer quantity" or "committed net injection" instead of "day-ahead price `p^{DA}`."
- Use `omega` consistently for price trajectory scenarios.
- Use `hat p^{PV}` for PV availability and `p^{PV}` for dispatched PV.

## 6. Proposition 2: Exactness, Proof, and Experiment Verification

Reviewer 5 identifies Proposition 2 as central and restrictive. This must be handled carefully.

### 6.1 Rename Proposition 2

Current title:

> Decoupling of the second-stage problem

Recommended title:

> Exact max-min decoupling under price-order preservation

This title makes the mathematical mechanism clear.

### 6.2 Strengthen the Statement

Use assumptions rather than embedding everything in the proposition:

```latex
Assumption 2 (PV boundary and price-order preservation).
For each sampled price trajectory `omega`,
(i) `lambda^{DA}_{t,omega} - c^{PV} >= kappa` for all `t`;
and (ii) for any `t,t'`,
`lambda^{DA}_{t,omega} > lambda^{DA}_{t',omega}` implies
`lambda^{DA}_{t,omega} - kappa >= lambda^{DA}_{t',omega} + kappa`.
```

Then Proposition 2:

> Under Assumption 2, the optimal PV dispatch is at the realized availability upper bound, and there exists a worst-case PV availability profile obtained by solving the linear problem (33). Conditional on this profile, the recourse problem reduces exactly to (34).

### 6.3 Strengthen the Proof

The proof should have four explicit steps:

1. **No curtailment.**
   From `lambda^{DA}_{t,omega} - c^{PV} >= kappa`, all PV coefficients in each affine piece are nonpositive. Thus increasing PV dispatch weakly reduces cost, so `p^{PV}_t = hat p^{PV}_t`.

2. **Coefficient ordering.**
   For each `t`, the possible PV coefficients lie in an interval:

```latex
[c^{PV} - \lambda^{DA}_{t,\omega} - \kappa,\,
 c^{PV} - \lambda^{DA}_{t,\omega} + \kappa].
```

The price-order preservation condition ensures these intervals do not overlap in a way that changes the ordering. Therefore the worst-case PV allocation over the budgeted set depends only on price ordering, not on the active storage/imbalance segment.

3. **Worst-case PV selection.**
   The outer maximization is a continuous knapsack problem over the budgeted uncertainty set. Because the coefficient ordering is fixed, one worst-case profile can be selected by (33).

4. **Reduced storage dispatch.**
   Once that profile is fixed, the remaining recourse problem is the separable PWL storage dispatch problem over nested cumulative constraints.

### 6.4 Add Validity Table in Case Study

Add a table verifying all assumptions used in Proposition 2:

| Scenario set | min price | min price minus PV cost | `kappa` | min positive price spread / 2 | Exactness condition |
|---|---:|---:|---:|---:|---|
| 25 | fill | fill | fill | fill | satisfied |
| 100 | fill | fill | fill | fill | satisfied |
| 250 | fill | fill | fill | fill | satisfied |
| 500 | fill | fill | fill | fill | satisfied |

If any row fails, do not hide it. State that those runs should not be used for exactness claims, or revise `kappa` / scenario states so the condition holds.

### 6.5 Negative Price Discussion

Add this to Section 6 or immediately after Proposition 2:

> Negative prices are outside the exactness domain of Proposition 2. When prices are negative or below the PV marginal cost plus the penalty spread, curtailment can be optimal and the worst-case PV realization may depend on the storage dispatch. The original max-min recourse model remains valid, but the decoupled LP and the local-search oracle are no longer guaranteed to be exact. In such regimes, one should solve the original recourse problem or develop a different oracle that allows PV curtailment.

This directly answers Reviewers 2, 4, and 5.

## 7. Local Search Algorithm and Proofs

The current proof section needs serious revision. This is not only a presentation issue; there are logical inconsistencies.

### 7.1 Current Problems To Fix

1. Stray text appears before Proposition 3:

```text
exist single optimal solution
```

Remove it.

2. The monotonicity direction is inconsistent:

- Proposition 3 says the sequence is componentwise nonincreasing.
- Algorithm 1 says `Delta e_t <- Delta e_t - increment`, which suggests decreasing `Delta e_t`.
- Appendix F says the sequence is componentwise nondecreasing and bounded above.

Choose one sign convention and make every statement consistent.

3. Algorithm initialization is inconsistent:

- The paragraph says initialize `Delta e_t = bar e - e_0` for many `t`.
- Algorithm 1 says initialize all `Delta e_t = 0`, then set `Delta e_1 = bar e - e_0` and `Delta e_T = e_0 - bar e`.

This must be corrected before submission.

4. Equation (28) appears vulnerable to notation confusion. It should clearly state:

```latex
\underline e - e_0 \le \sum_{\tau=1}^t \Delta e_\tau
\le \bar e - e_0, \quad t=1,\ldots,T-1,
\qquad
\sum_{\tau=1}^T \Delta e_\tau = 0.
```

5. Algorithm 1 line 5 should use the correct margin formula. If updating `Delta e_t` changes all future cumulative sums, the feasible step is the minimum residual segment length and the minimum future slack to the relevant lower/upper cumulative bounds.

### 7.2 More Defensible Proof Structure

Replace the current proof chain with this structure:

1. **Lemma 1: Existence of optimal second-stage solution.**
   The feasible set `Y^e` is nonempty, closed, and bounded; the objective is continuous PWL. Therefore an optimum exists.

2. **Lemma 2: Equivalence to separable convex PWL resource allocation.**
   After Proposition 2, the recourse problem has the form:

```latex
\min_{\Delta e}
\sum_{t=1}^T f_t(\Delta e_t)
\quad
\text{s.t. }
L_t \le \sum_{\tau=1}^t \Delta e_\tau \le U_t,
\quad
\sum_{t=1}^T \Delta e_t=0.
```

3. **Lemma 3: Greedy exchange property.**
   For separable convex PWL objectives over nested cumulative constraints, shifting an admissible amount from a higher marginal-cost segment to a lower marginal-cost segment cannot increase the objective. This is where the resource-allocation literature should be cited.

4. **Proposition 3: Feasibility invariant.**
   Each algorithmic update preserves cumulative constraints and terminal equality by construction.

5. **Proposition 4: Finite termination.**
   Each update either exhausts a PWL segment or activates a cumulative constraint. There are finitely many segments and constraints, so the algorithm terminates finitely.

6. **Theorem 1: Global optimality.**
   At termination, no feasible exchange from a higher marginal-cost active segment to a lower marginal-cost residual segment exists. By the greedy optimality condition for nested resource allocation, the terminal solution is globally optimal.

This is stronger and cleaner than the current contradiction proof.

### 7.3 Literature To Add for the Proof

Use the local notes:

- Vidal, Gribel, and Jaillet (2019), *Separable Convex Optimization with Nested Lower and Upper Constraints*.
- Halman, Nannicini, and Orlin (2018), *On the complexity of energy storage problems*.
- The greedy resource-allocation paper noted in `notes/notes.txt`: *The Greedy Procedure for Resource Allocation Problems: Necessary and Sufficient Conditions for Optimality*. Add the exact bibliographic entry and cite the theorem/proposition number after checking the paper.

Do not merely cite these papers generally. Follow the writing guidance: cite the exact theorem/proposition/page supporting the greedy optimality condition.

## 8. PSM-PAVA Convergence and Algorithmic Guarantee

Reviewer 5's concern is valid. The current paper says BB step size, clipping, backtracking, nonmonotone updates, and relative objective stopping, but does not prove an optimality guarantee.

### 8.1 Efficient Fix

Do not try to prove global convergence for the exact implemented BB/backtracking heuristic unless you are certain the nonsmooth Armijo argument is valid. Instead:

1. Add a theoretical projected subgradient variant with standard step sizes.
2. Present BB/backtracking as an acceleration used in experiments.
3. Add empirical optimality validation against exact/outer-approximation baselines.

Suggested text:

> For theoretical completeness, if the projected subgradient method is run with a standard diminishing step-size rule and bounded subgradients, the best objective value converges to the optimal value of the convex stochastic LP. In the computational experiments, we use a clipped Barzilai-Borwein step size with backtracking as a practical acceleration. Therefore, the convergence guarantee applies to the standard projected subgradient variant, while the accelerated variant is validated numerically by comparison with LP and cutting-plane baselines.

### 8.2 Add a Theorem

Add a theorem after Section 4.2:

> Because `X` is compact and the recourse value is convex PWL, the objective is convex and Lipschitz on `X`. For projected subgradient iterates with step sizes satisfying standard conditions, the best incumbent objective converges to the optimal objective. With a fixed horizon step size, the averaged iterate satisfies the standard `O(1/sqrt(K))` suboptimality bound.

Do not overstate this. The guarantee is for the standard subgradient scheme, not necessarily for every BB step-size variant.

### 8.3 Revise Stopping Criterion

Current stopping rule:

```latex
|Phi(p^{k+1}) - Phi(p^k)| / |Phi(p^k)| <= epsilon_rel
```

This is not an optimality certificate. Add:

- report best incumbent objective;
- optionally report a benchmark gap against deterministic equivalent / Benders / CC&G for tested instances;
- state that the relative objective-change rule is a practical termination criterion.

Suggested text:

> The relative objective-change criterion is used as a computational stopping rule and should not be interpreted as a duality gap. Solution quality is assessed by comparison with benchmark LP and cutting-plane solutions in Section 5.

## 9. Benchmarks: Benders / L-shaped / CC&G

Reviewer 5 asks for Benders or L-shaped decomposition. The current manuscript already has a controlled comparison where CC&G is applied to the same stochastic LP instance. This can be reframed, but it must be clearer.

### 9.1 Lowest-Change Solution

Rename and explain the "CC&G on stochastic LP" benchmark as a Benders-style outer-approximation / L-shaped cutting-plane method, if the implemented cuts are value-function cuts from recourse evaluations.

Suggested wording:

> For the reformulated stochastic LP, the natural decomposition benchmark is a Benders/L-shaped outer-approximation method, which iteratively approximates the recourse value by cuts. The CC&G implementation applied to the stochastic LP is exactly this cut-accumulation outer-approximation benchmark in our setting. We therefore report it as "Benders/L-shaped OA" in Table 4.

Only use this wording if technically true for the implementation. If the implementation is not actually Benders/L-shaped, then add a small Benders baseline or avoid saying "exactly."

### 9.2 Better But Still Minimal Experiment

Add one additional column to Table 4:

- `Benders/L-shaped OA on stochastic LP`

If the existing "Stochastic LP problem - CC&G" column is the same algorithm, rename it. No new simulation needed.

If new runs are feasible, run only for 25, 100, and 250 scenarios. The reviewer needs evidence, not a full new study.

### 9.3 Be Careful With Speedup Claims

Current claims like `122x-204x` and `2116x-3689x per iteration` invite skepticism. Keep the data, but tone down the prose:

- Say "in the tested instances."
- Say "relative to the implemented outer-approximation baseline."
- Avoid "state-of-the-art algorithm" unless the baseline is clearly standard and fairly implemented.

## 10. Literature Review Revision

Reviewer comments show that the literature review is a major weakness. This can be fixed without changing the model.

### 10.1 New Literature Review Structure

Use four focused paragraphs:

1. **VPP offering and storage bidding under uncertainty.**
   Discuss day-ahead / real-time offering, storage arbitrage, and VPP bidding. This should be the anchor, not generic RO.

2. **Uncertainty modeling choices.**
   Compare deterministic, stochastic programming, robust optimization, adaptive robust optimization, distributionally robust optimization, flexible robust optimization, and multi-stage formulations.

3. **Algorithmic bottleneck.**
   Explain scenario explosion, max-min recourse, MILP recourse, Benders/L-shaped/CC&G outer approximation, and master-problem growth.

4. **Structured resource allocation algorithms.**
   Discuss separable convex optimization with nested constraints and energy storage complexity, then explain why existing algorithms do not provide the value-and-subgradient oracle needed for first-stage projected subgradient updates.

### 10.2 Citations To Keep or Emphasize

From the current reference list, likely useful:

- Rintamaki et al. (2020): strategic offering in day-ahead/intraday markets.
- Kraft et al. (2023): stochastic trading strategies in sequential electricity markets.
- Kim and Choi (2024): sample robust VPP bidding.
- Kim et al. (2022): VPP bidding applied analytics.
- Baringo and Conejo (2011): robust offering strategy.
- Attarha et al. (2019): robust bidding for solar plus battery.
- Sunar and Birge (2019): renewable energy day-ahead markets.
- Krishnamurthy et al. (2018): storage arbitrage under DA/RT uncertainty.
- Halman et al. (2018) and Vidal et al. (2019): resource allocation / storage structure.

### 10.3 Citations To Reconsider

Reviewer 2 explicitly questions:

- Sinha et al.
- Kleinert et al.
- Beck et al.
- the use of non-peer-reviewed references.
- Baringo and Baringo (2018) if cited for VPP offering.

Recommendation:

- Remove Sinha, Kleinert, and Beck unless the paper genuinely discusses bilevel market-power models. If price-taking is adopted, bilevel citations distract from the contribution.
- Replace Baringo and Baringo (2018) with VPP/offering-specific works when discussing offering under uncertainty.
- Keep arXiv/preprint references only if essential and clearly labeled with version/date. Do not use a preprint as a central support for a major modeling assumption if a peer-reviewed alternative exists.
- Correct any statement about "alternating direction method" related to Bertsimas et al. unless the cited paper actually uses that method.

### 10.4 Research Gap Statement

Add a paragraph like this at the end of the literature review:

> Existing VPP offering studies provide stochastic, robust, and adaptive-robust formulations, but their solution methods typically rely on general-purpose MILP, Benders/L-shaped decomposition, or CC&G-type outer approximation. Separately, resource-allocation algorithms exploit nested storage constraints efficiently, but they are not designed as recourse oracles that also return first-stage subgradients for stochastic adaptive-robust offering. The gap addressed here is therefore the design of an exact value-and-subgradient oracle for a structurally specified VPP recourse problem and its integration with monotone offer-curve projection.

This is the paper's strongest OR positioning.

## 11. Case Study and Figures

### 11.1 Add a Schematic Diagram

Reviewer 1 explicitly asks for it.

Add Figure 1 before the formulation:

Elements:

- VPP aggregator.
- PV portfolio, storage fleet, aggregated load.
- Day-ahead offer curve submission.
- ISO/RTO market clearing.
- Exogenous day-ahead price trajectory.
- PV availability uncertainty set.
- Real-time dispatch and imbalance settlement.
- Arrows showing first-stage offer and second-stage operation.

Also add a timing line:

1. Before day-ahead clearing: submit offer curves.
2. Day-ahead clearing: price vector and committed quantities determined.
3. Operating day: PV availability realized; storage dispatch responds.
4. Settlement: day-ahead revenue plus imbalance cost.

This answers R1 and R3 simultaneously.

### 11.2 Add Demand Data To Table 2

Reviewer 4 asks why aggregated demand data is not provided.

Add:

- average load,
- peak load,
- minimum load,
- source,
- whether deterministic forecast is used.

Example columns:

| Component | Number of units | Power rating | Energy rating | Profile used | Data source |

For aggregated load:

- Number of units: aggregated.
- Power rating: peak/average demand from data.
- Energy rating: not applicable.
- Profile used: 24-hour deterministic load forecast.

### 11.3 Show Real-Time Settlement Difference From Day-Ahead Operation

Reviewer 4 asks that real-time settlement and its difference from day-ahead operation be shown clearly.

Minimal new figure/table:

Add a small table for 3 representative scenarios:

| Scenario | DA revenue | PV cost | storage degradation | imbalance settlement | total profit | total imbalance |
|---|---:|---:|---:|---:|---:|---:|

Add a plot:

- DA committed quantity.
- realized aggregate PV plus storage dispatch.
- mismatch `p^{mis}`.
- imbalance cost.

If no new simulations are desired, use existing economic dispatch outputs underlying Figure 8.

### 11.4 Clarify "Coupled" Uncertainty

Reviewer 4 notes that the conclusion says coupled price and renewable uncertainty, but it is unclear how they are coupled.

Two options:

1. If there is no statistical coupling, stop saying "coupled."
   Replace with "jointly considered" or "interacting through the recourse function."

2. If you want to keep "coupled," explain:
   Price and PV are not modeled with a joint distribution, but they are operationally coupled because the price scenario determines the worst-case PV profile in (33) and the storage dispatch in (34).

Recommended wording:

> The model jointly considers price and PV uncertainty. The two sources are not modeled through a joint probability law; rather, they interact through the second-stage value function because each price trajectory induces a corresponding worst-case PV availability profile and dispatch cost.

## 12. Presentation and Writing Fixes

### 12.1 Remove Consecutive Headings

Add transition paragraphs between major headings and subheadings. Each paragraph should explain:

- what the section does,
- why it is needed,
- how it connects to the previous section.

### 12.2 Fix Known Typos

At minimum:

- "Tractable second-stage problem is derived" -> "A tractable second-stage problem is derived."
- "instead, follow the market rules" -> "instead, following the market rules"
- "Given day-ahead price `p^{DA}`" -> "Given the day-ahead offer quantity `p^{DA}`" or "Given the committed net injection"
- "is presents" -> "are presented"
- "it provide" -> "it provides"
- "note that, the" -> "note that the"
- Search for "the later lower" and correct the phrase if present.

### 12.3 Citation Format

Reviewer 5 notes sentence-level citation issues. Fix throughout:

- Bad: "at the distribution level IEA (2022)"
- Good: "at the distribution level (IEA, 2022)"

Also avoid unsupported broad claims. If a claim is not directly supported by the previous sentence or citation, add a citation or remove the claim.

### 12.4 Mathematical Writing

Follow the writing-style PDF:

- State where each assumption is used.
- Cite theorem/proposition numbers when relying on external mathematical results.
- Avoid ambiguous "where ..." notation after long formulas.
- Break long proofs into lemmas.
- Do not start sentences with symbols.
- Avoid "obviously," "clearly," and "it is easy to see" unless a reason is supplied.

## 13. Reviewer-by-Reviewer Coverage Matrix

### Reviewer 1

| Comment | Required revision |
|---|---|
| Need schematic diagram | Add VPP/market/uncertainty/timing schematic before formulation |
| Novelty insufficient | Rewrite contribution around exact value-and-subgradient oracle, not generic RO/PSM/greedy |
| Theory limited | Strengthen Proposition 2, local-search optimality theorem, and PSM convergence discussion |

### Reviewer 2

| Comment | Required revision |
|---|---|
| No conventional generation | Add scope assumption and explain convex DER portfolio focus |
| `e_T=e_0` unjustified | Add daily-cycle neutrality justification and limitation |
| Positive prices unrealistic | Add exactness-domain assumption, negative-price limitation, and experiment validity table |
| No distribution network | Add single-node settlement assumption and limitation |
| Price-taker not discussed | Add explicit price-taking assumption |
| Imbalance settlement unsupported | Reframe as reduced-form penalty spread; add calibration explanation |
| Literature review off-target | Rewrite literature review around VPP offering and uncertainty methods |
| Wrong/irrelevant citations | Remove or replace Sinha/Kleinert/Beck; check Baringo citation |
| Title incomplete | Retitle with stochastic adaptive-robust / price-taking language |
| Formula (13) incomplete | Add scenario-to-committed-quantity mapping |
| VPP description inconsistent | Use aggregate portfolio variables and aggregation equations |
| Presentation problems | Fix headings, typos, grammar |

### Reviewer 3

| Comment | Required revision |
|---|---|
| Hybrid uncertainty unjustified | Add rationale and PV uncertainty calibration |
| Two-stage timing unclear | Add timing diagram and clarify Markov scenarios are not sequential decisions |
| Applicability too limited | Add "exactness domain and limitations" subsection |
| CC&G motivation unclear in abstract | Remove or clarify CC&G sentence |
| Markov price concerns | Reframe Markov as temporal-correlation scenario generator |
| Nonanticipativity misuse | Replace with explicit offer-curve mapping |

### Reviewer 4

| Comment | Required revision |
|---|---|
| Why only PV/storage/load | Explain convex DER portfolio scope and binary-resource limitation |
| Uncertainty review incomplete | Add SP/RO/ARO/DRO/flexible robust/multi-stage comparison |
| Lower prices and curtailment | Add negative-price limitation and exactness-condition discussion |
| Real-time settlement unclear | Add DA/RT settlement decomposition table or figure |
| "Coupled" uncertainty unclear | Replace or define "coupled" precisely |
| Demand data missing | Add demand statistics to DER data table |
| Equation references wrong | Full cross-reference audit |

### Reviewer 5

| Comment | Required revision |
|---|---|
| Hybrid model insufficiently motivated | Add rationale and calibration |
| Literature gap unclear | Add explicit research gap paragraph |
| Proposition 2 restrictive | Add assumption table, validity table, and violation discussion |
| PSM-PAVA convergence incomplete | Add standard projected subgradient theorem; classify BB as acceleration |
| Need Benders/L-shaped benchmark | Rename/clarify stochastic-LP CC&G as Benders/L-shaped OA if valid, or add small baseline |
| Minor grammar/citation issues | Fix listed items and citation format |

## 14. Minimal Revision Plan With Least Re-Simulation

### Must Do, No Re-Simulation

1. Rewrite title, abstract, contribution, and conclusion.
2. Add "Modeling Scope and Structural Assumptions" table.
3. Revise Section 2.3 to justify hybrid uncertainty and PV set calibration.
4. Revise Markov price language and remove misleading nonanticipativity statement.
5. Add explicit mapping from price state to committed offer quantity.
6. Reframe imbalance settlement as reduced-form penalty spread.
7. Rewrite Proposition 2 statement and proof.
8. Rewrite local-search proof with consistent sign convention and known resource-allocation support.
9. Add standard projected subgradient convergence theorem and weaken BB/backtracking claims.
10. Rewrite literature review around the gap.
11. Fix VPP aggregation inconsistency.
12. Fix grammar, citations, equation references, and heading transitions.

### Small Checks, No Full Re-Simulation

1. Compute and report assumption-validity statistics from existing scenario files.
2. Add demand statistics from existing load data.
3. Rename existing stochastic-LP CC&G benchmark as Benders/L-shaped OA if technically accurate.
4. Add a DA/RT settlement decomposition using existing Figure 8 outputs if available.

### Optional Small New Experiments

Only if time permits:

1. Benders/L-shaped baseline for 25, 100, and 250 scenarios.
2. `kappa` sensitivity table.
3. Negative-price stress test in an appendix, explicitly showing when exactness conditions fail.

The optional negative-price test should not be framed as a performance claim. It should be framed as a boundary-of-validity demonstration.

## 15. Recommended New Manuscript Outline

1. Introduction
   - Motivation
   - Literature review
   - Research gap
   - Contributions
2. Problem Statement
   - Market timeline and price-taking setting
   - Aggregate VPP model
   - Structural assumptions table
   - Uncertainty modeling and calibration
   - Two-stage stochastic adaptive-robust formulation
3. Structural Reformulation
   - Complementarity redundancy under positive imbalance rates
   - PV boundary and price-order preservation
   - Exact max-min decoupling
   - Validity domain and violation discussion
4. Solution Method
   - Second-stage resource allocation form
   - Local-search value-and-subgradient oracle
   - Proof of finite global optimality
   - PAVA projection
   - Projected subgradient theory and accelerated implementation
5. Case Study
   - Data and assumption-validity table
   - Local-search vs LP solver
   - PSM-PAVA vs Benders/L-shaped OA
   - Offering and DA/RT settlement interpretation
   - Out-of-sample analysis
6. Discussion and Limitations
   - Negative prices
   - Dispatchable generators/binaries
   - Network constraints
   - Multi-stage recourse
7. Conclusion

If journal page limits are tight, merge Discussion and Limitations into the end of Section 5 and the conclusion.

## 16. Acceptance-Risk Assessment

After revision, the remaining risk is not the computation. Reviewers already found the experiments impressive. The risk is whether the contribution is perceived as too narrow for EJOR.

To reduce that risk, the revised paper must repeatedly communicate:

- The paper does not claim to solve all VPP offering problems.
- The paper identifies a mathematically precise subclass.
- The exactness conditions are checkable.
- The algorithm is not just "greedy"; it is a value-and-subgradient oracle for nested separable recourse.
- The limitations are openly stated, not hidden.

The strongest one-sentence positioning is:

> We contribute an exact structure-exploiting value-and-subgradient oracle for a checkable class of stochastic adaptive-robust VPP offering problems, enabling first-order solution of large scenario sets without accumulating recourse-variable copies as in outer-approximation methods.

Use this idea in the abstract, introduction, and conclusion.
