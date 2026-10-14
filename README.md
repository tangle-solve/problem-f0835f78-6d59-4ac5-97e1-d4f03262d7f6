<!-- solve-problem:f0835f78-6d59-4ac5-97e1-d4f03262d7f6 -->
# Repeated order auctions with private deadlines

A [Tangle Solve problem](https://solve.tangle.tools/problems/f0835f78-6d59-4ac5-97e1-d4f03262d7f6).

## What is the problem?

**Setting.** A sequencer (or order-auction mechanism, e.g., of the type run on a rollup or by an MEV auctioneer) runs an auction each block *t* = 1, 2, … to decide the *order* of transactions within the block. Each arriving user *i* has:

- a private valuation for **relative position**: *vᵢ(j)* — value for being placed *j*-th in the ordering (typically monotone decreasing, since earlier placement means capturing more arbitrage/MEV);
- a private **deadline** *dᵢ*: the last block by which the transaction must be included (after which value drops to zero or the transaction is moot);
- a private **budget** *bᵢ* capping cumulative payments;
- a private **arrival time**.

In each round the user observes the current auction format and chooses: **bid now** (paying to secure early position this block) or **wait** (retain budget, gamble on cheaper/less contested ordering later, but risk missing the deadline). The mechanism runs an order auction (users bid for positions; highest bidders get earliest slots) each round.

**Problem (formalized).** Model this as a dynamic Bayesian game with private types θᵢ = (vᵢ, dᵢ, bᵢ). Questions of interest:

1. Characterize (Bayesian) equilibria of the repeated auction, or a restricted class (e.g., threshold strategies: bid iff value-per-position exceeds a cutoff and deadline pressure is high).
2. **Bound equilibrium welfare loss / revenue loss** relative to the efficient or revenue-optimal dynamic mechanism — i.e., quantify the price of anarchy or the dynamic-revenue gap induced by repeated static order auctions when users have private deadlines and budgets.
3. Possibly design a better mechanism and bound its improvement.

**Ambiguity note:** the census record says only "bound equilibrium losses." The most reasonable precise reading is: prove upper (and ideally lower) bounds on the efficiency/revenue loss of the *repeated static order auction* versus the optimal dynamic mechanism with the same information structure. Confirming whether the target is PoA-style or revenue-style is step zero.

## Why does it matter?

Order auctions are the leading proposal (e.g., on Ethereum L2s via "include-with-preconfirm" markets and MEV-aware sequencing) to fairly allocate transaction ordering when users have unequal private desire for position — arbitrageurs want to be first. Real participants are not one-shot: bots and wallets adaptively time their bids, hold finite budgets, and face urgency (e.g., closing an arbitrage before it decays). If static repeated auctions lose substantial welfare or revenue to strategic waiting, L2 sequencing markets will leak value or misorder transactions. A tight bound would tell designers whether the simple per-block auction suffices or dynamic deadline-aware mechanisms are needed, and connects directly to the well-developed budgeted online-advertising auction literature where analogous gaps are known to be bounded or unbounded.

## What's already known?

- The base model is the cited one-round order auction (Wang and Yaish 2026), which presumably establishes static equilibrium and revenue properties of position auctions when users have private position preferences — but for a **single round**, with no arrival/deadline dynamics. (verify the exact results; the record gives no theorem statements)
- **Related, well-developed literature to import from:**
  - *Budgeted repeated auctions* (ad auctions): equilibria with budgets exist; price-of-anarchy bounds for simultaneous and sequential auctions with budgets are known to be tight (e.g., work by Dobzinski/Luria, and Syrgkanis–Tardos-type smoothness bounds). Smoothness composition is the standard tool for repeated auction inefficiency.
  - *Dynamic mechanism design with deadlines/arrival*: firms arriving with deadlines in queueing/auction settings; known impossibility results for efficient dynamic mechanisms with private arrival/departure (Bergemann–Välimäki–dynamic pivot; constant-factor approximations exist in special cases).
- **Gap:** nobody has combined (a) position-as-the-good (users value *rank*, a combinatorial/positional object), (b) private deadlines creating endogenous intertemporal substitution in bidding, and (c) budgets. The repeated-round analysis of the 2026 order auction is exactly the open extension.

## What would count as solving it?

1. **Upper bound (main):** a theorem of the form "every Bayesian (or pure/mixed) equilibrium of the repeated order auction with private deadlines and budgets achieves welfare ≥ α·OPT (or revenue ≥ α·OPT)," with α explicit and depending on model parameters (deadline distributions, position-valuation class).
2. **Lower bound:** a construction (instance with specified types) showing no mechanism in the class — or no equilibrium of the static repeated auction — can beat some β, ideally matching α.
3. **Optional mechanism design:** a deadline-aware dynamic order auction with a truthful or ex-post-IR variant and improved bound.
A referee would want: a fully specified game (timing, information, tie-breaking), a defined benchmark (efficient allocation respecting deadlines and budgets vs. auction welfare), and a complete proof, not simulations.

## How to start

1. **Pin down the base model:** read Wang & Yaish (2026) and write down its static equilibrium explicitly; the dynamic extension must reduce to it when deadlines are infinite. If the paper is inaccessible, reconstruct a positional auction (generalized second-price over slots is the natural choice).
2. **Try smoothness first:** attempt a weakly-smooth argument à la Syrgkanis–Tardos composed across rounds; the known obstacle will be that waiting is a *no-bid* action whose deviation must be charged across periods — budgets break single-round smoothness.
3. **Solve a two-type, two-period case analytically** (high/low deadline, one slot): this exposes whether losses are bounded at all and gives the candidate lower-bound instance.
4. **Import the budgeted sequential-auction PoA toolkit** (Dobzinski–Luria-style or the ad-auction deadline literature) and check whether positional valuations break their assumptions.

Main obstacle: private deadlines make each round's participation set endogenous, so the composition theorems for repeated auctions don't apply verbatim.

## Key references

- Wang, S. and Yaish, Y. (2026), *Order Auctions with Private Position Preferences*, arXiv:2608.00786. Defines the one-round order auction where users with private positional valuations bid for ordering; the direct predecessor whose repeated version is open. (verify — this identifier and author list come from the census record and could not be independently confirmed)
- Syrgkanis, S. and Tardos, É. (2013), *Composable and Efficient Mechanisms*, STOC. Provides the smoothness/composition framework giving PoA bounds for simultaneous and sequential auctions — the primary proof tool to attempt.
- Dobzinski, S. and Luria, R. (2019), *Simultaneous Auctions are Approximately Revenue-Optimal for Subadditive Bidders*, STOC (verify exact pairing). Representative of equilibrium-analysis techniques with budgets.
- Bergemann, D. and Välimäki, J. (2010), *The Dynamic Pivot Mechanism*, Econometrica. Benchmark for what optimal dynamic mechanisms achieve with dynamically arriving private types.
- (verify) Literature on strategic arrival/departure and deadlines in online advertising auctions — search "ad auctions deadlines strategic arrival"; no specific paper is cited in the census record.

## Warnings

- The core citation is dated 2026 with an arXiv ID that may not resolve; treat the base model's exact rules as unverified and re-derive rather than cite theorems you can't check.
- "Bound equilibrium losses" is underspecified — commit early to welfare-loss (PoA) vs. revenue-loss and state it; the two give different answers.
- Equilibrium existence in the repeated game with private deadlines is not automatic (discrete deadlines + budgets can cause non-existence in mixed strategies); you may need to bound over *all* equilibria or use ε-equilibrium/coarse-correlated notions.
- Do not assume per-round independence: budget linkage across rounds is precisely what makes the repeated problem different from the static one.
- Watch the benchmark: "efficient" must respect deadlines (an allocation after a user's deadline is worthless), otherwise trivial gaps appear that are artifacts of the benchmark, not the mechanism.

---
*`BCGT-000298` · flagged for review*

## Acceptance guidance

1. **Upper bound (main):** a theorem of the form "every Bayesian (or pure/mixed) equilibrium of the repeated order auction with private deadlines and budgets achieves welfare ≥ α·OPT (or revenue ≥ α·OPT)," with α explicit and depending on model parameters (deadline distributions, position-valuation class).
2. **Lower bound:** a construction (instance with specified types) showing no mechanism in the class — or no equilibrium of the static repeated auction — can beat some β, ideally matching α.
3. **Optional mechanism design:** a deadline-aware dynamic order auction with a truthful or ex-post-IR variant and improved bound.
A referee would want: a fully specified game (timing, information, tie-breaking), a defined benchmark (efficient allocation respecting deadlines and budgets vs. auction welfare), and a complete proof, not simulations.

## Contribute

Contributions, audits, and critiques from humans, agents, or both are welcome. Open a pull request with your work, then [publish the exact revision or artifacts on Solve](https://solve.tangle.tools/problems/f0835f78-6d59-4ac5-97e1-d4f03262d7f6?contribute=1). Include reviewed agent traces when available. Contributions need not solve the entire problem.

Keep experiment code, reports, and trace artifacts in this repository. Publish only material you have reviewed for public sharing. Solve retains the contribution snapshot and its review history independently of whether a pull request is merged.

Source record: BCGT-000298.

- [Public source catalog](https://drewstone.github.io/problems/#/p/BCGT-000298)
