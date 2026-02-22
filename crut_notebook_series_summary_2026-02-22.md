# CRUT Monte Carlo Analysis — Notebook Series Summary
## *When Do Charitable Remainder Unitrusts Outperform? A Monte Carlo Analysis*
### Klaus Gottlieb, JD, MS, MBA — Wealth Care Lawyer, Cayucos, CA
### Series Date: February 22, 2026

---

## Overview

This document summarizes the purpose, methodology, and findings of each notebook in the seven-notebook series analyzing when Charitable Remainder Unitrusts (CRUTs) outperform a taxable hold-and-liquidate benchmark. The series uses paired-path Monte Carlo simulation with 10,000 paths per scenario, a corrected actuarial engine implementing Gottlieb (2025) Equations 1–6 and Table 2010CM, and a consistent baseline representing a Two Life 63/65 donor couple contributing a $1M low-basis (20%) appreciated asset to a 6% payout CRUT in California.

**Baseline parameters (all notebooks):**

| Parameter | Value |
|---|---|
| FMV of contributed asset | $1,000,000 |
| Cost basis | 20% ($200,000) |
| Donor ages | 63 and 65 (Two Life) |
| Payout rate | 6% |
| §7520 rate | 5.0% |
| Expected return μ | 7% |
| Volatility σ | 12% |
| State tax rate | 9.3% (California) |
| Federal ordinary rate | 37% |
| Federal LTCG + NIIT | 23.8% |
| Benchmark turnover (primary) | 20% |
| Monte Carlo paths | 10,000 |
| Simulation horizon | 25 years (IRS actuarial) |

**Corrected baseline outputs (all notebooks):**

| Metric | Value |
|---|---|
| Charitable deduction | $240,220 |
| Remainder factor R | 0.2402 |
| PV tax benefit (OBBBA-capped) | $104,514 |
| Win probability vs. hold-liquidation (20% turnover) | 41.6% |
| IRS actuarial horizon | 24.6 years |

---

## Notebook 00 — Foundation
**File:** `notebook_00_foundation_2026-02-22.ipynb`

### What Was Examined
The foundational mechanics of the CRUT vs. taxable benchmark comparison, including the actuarial valuation engine, the simulation architecture, and a 2×2 factorial decomposition explaining the central paradox of the corrected engine.

### Why
Before any parameter sensitivity analysis can be trusted, the underlying engine must be verified against an authoritative external source (CalCRUT.com) and the core architectural choices must be documented and defended. NB00 also resolves a counterintuitive finding that emerges from the corrected engine: the deduction nearly doubles compared to the prior engine, yet win probability falls. Without explanation, this would undermine the entire series.

### How It Was Done
- **Actuarial engine construction:** Implements the exact summation method of Treas. Reg. §1.664-4(e)(5)(i) using Table 2010CM mortality data. F-factor computed per Reg. §1.664-4(e)(6)(ii) (Equation 1 of Gottlieb 2025). Remainder factor via probability-weighted summation over all ages to 110 (Equations 4 and 6). Cross-validated against CalCRUT.com to six decimal places.
- **Simulation architecture:** Paired-path Monte Carlo — both CRUT and benchmark use identical return paths, eliminating sampling noise from the comparison. Benchmark is hold-liquidation with annual capital gains drag on realized turnover and terminal liquidation tax.
- **OBBBA cap:** Deduction tax benefit capped at 35% federal ordinary rate for top-bracket filers, matching the One Big Beautiful Budget Act provision.
- **2×2 factorial decomposition:** Four combinations of (old deduction / corrected deduction) × (old horizon 33yr / corrected horizon 25yr) isolate the paradox components quantitatively.

### Outcome
- Actuarial engine validated: remainder factor 0.240220 matches CalCRUT.com exactly.
- The central paradox resolved: the deduction effect (+$45,965 mean delta, +9.2 pp win prob) is dominated by the horizon effect (−$95,809 mean delta, −25.9 pp win prob). Ratio: 2.1×. The shorter horizon cuts the CRUT's income stream harder than the benchmark's terminal accumulation, because the CRUT is structurally a distribution vehicle while the benchmark is a terminal-value vehicle.
- Baseline win probability: 41.6% at 20% turnover (corrected engine, n=10,000).

---

## Notebook 01 — Parameter Sensitivity (OAT Analysis)
**File:** `notebook_01_sensitivity_2026-02-22.ipynb`

### What Was Examined
One-at-a-time (OAT) sensitivity analysis of all 12 parameters governing CRUT performance. Each parameter is swept across its realistic planning range while all others are held at baseline, producing a win probability range (in percentage points) and a sensitivity ranking.

### Why
A practitioner advising a client cannot optimize 12 parameters simultaneously. The OAT ranking identifies which parameters dominate — where attention and due diligence produce the most impact — and which can be treated as second-order. The ranking also provides the organizational backbone for the remaining notebooks (NB02–NB06), each of which examines a top-ranked parameter in depth.

### How It Was Done
- Each of 12 parameters swept across its planning-relevant range at four turnover levels (0%, 20%, 40%, 60%).
- Win probability computed at each sweep point.
- Range (max win prob − min win prob, in pp) used as the sensitivity metric.
- Ranking tables auto-generated in code to update dynamically on re-run.
- A second ranking table added for the 60% turnover scenario to show how rankings shift with benchmark aggressiveness.

### Outcome — OAT Rankings

| Rank | Parameter | Range (pp) |
|---|---|---|
| #1 | Expected return μ | ~55.7 |
| #2 | Longevity adjustment | ~47.5 |
| #3 | Benchmark fee | ~31 |
| #4 | Payout rate | ~29 |
| #5 | Asset basis | ~27 |
| #6 | Trust fee | ~24 |
| #7 | Volatility σ | ~18 |
| #8 | AGI (deduction utilization) | ~14 |
| #9 | Federal ordinary rate | ~12 |
| #10 | State tax rate | ~9 |
| #11 | Federal LTCG rate | ~6 |
| #12 | §7520 rate | ~0.8 |

**Key finding:** The §7520 rate ranks dead last — essentially zero sensitivity — across its full historical range (1.2%–8.2%). This is correct: the rate affects only the F-Factor at inception, and modest F-Factor sensitivity combined with the deduction representing only ~18% of total CRUT value produces negligible win probability impact.

---

## Notebook 02 — Asset Basis
**File:** `notebook_02_basis_2026-02-22.ipynb`

### What Was Examined
Asset basis (cost as a percentage of FMV) as a driver of CRUT attractiveness. Basis ranked #5 in the OAT analysis with a ~27 pp range. The notebook also examines the full benchmark hierarchy (hold-liquidation, hold-to-death, liq-reinvest) and their sensitivity to basis.

### Why
Basis is the most client-specific parameter in the analysis. Two clients identical in every other respect but with different basis percentages (10% vs. 80%) face fundamentally different CRUT economics. Practitioners advising on concentrated low-basis positions — the primary target client for CRUTs — need to understand this dependency quantitatively.

### How It Was Done
- Basis swept from 2% to 80% at four turnover levels.
- Three benchmark alternatives compared: hold-liquidation (primary), hold-to-death (§1014 step-up), liq-reinvest (immediate sale and reinvestment).
- Four-zone traffic light framework: zones defined by which benchmark the CRUT beats at which turnover assumption.
- Master parameter sensitivity ranking (basis within full 12-parameter context) serves as manuscript organizational reference.

### Outcome
- CRUT win probability rises sharply as basis falls: low-basis assets (2%–10%) are the natural CRUT clients.
- At 0% turnover (buy-and-hold benchmark), CRUT wins only ~40% of simulations even at very low basis — confirming that turnover is necessary for CRUT to be competitive.
- At 60% turnover, CRUT dominates across all basis levels above ~15%.
- The liq-reinvest benchmark (immediate sale and diversification) is the most relevant for concentrated position holders: CRUT competes strongly here because both strategies trigger the same immediate tax, but the CRUT then compounds tax-deferred.
- Note: The hold-to-death benchmark assumes §1014 step-up at death. If §1014 is repealed (a recurring legislative proposal), this benchmark collapses toward the hold-liquidation result, improving CRUT's relative position.

---

## Notebook 03 — Longevity
**File:** `notebook_03_longevity_2026-02-22.ipynb`

### What Was Examined
Longevity's effect on CRUT performance — ranked #2 in the OAT analysis with a 47.5 pp range. The notebook examines five dimensions: longevity adjustment sweep, starting age sweep, Single Life vs. Two Life comparison, longevity × return interaction, and four archetypal client profiles.

### Why
Longevity ranked second only to expected return, making it the most consequential variable a practitioner can assess prior to recommending a CRUT. Healthy clients with above-average life expectancy are substantially better CRUT candidates than clients of average or below-average health, holding all else equal.

### How It Was Done
- **Longevity adjustment sweep:** Simulation horizon shifted from −10yr to +15yr relative to the IRS actuarial expectancy. Deduction held fixed at IRS duration (it is fixed at inception by law — this is not an approximation).
- **Starting age sweep:** Younger donor aged 45–75, 2-year gap held constant. Reveals non-monotonic relationship between age and win probability.
- **Single Life vs. Two Life:** Matched ages compared across the full age range.
- **Interaction heatmap:** 2D grid of longevity adjustment × expected return μ.
- **Methodology note:** The deterministic-horizon design is formally defended via Jensen's inequality — f(T) is nearly linear over the mortality distribution's support, making the approximation error ~1–3 pp (within Monte Carlo noise). The sweep is superior to a stochastic integral because it preserves the full horizon-sensitivity curve rather than collapsing it to a scalar.

### Outcome
- Win probability is nearly linear in longevity adjustment: each additional year of life expectancy adds approximately 1.5–2.0 pp win probability across turnover levels.
- The non-monotonic age relationship is confirmed: there is an optimal starting age range (approximately 58–68) where the horizon is long enough to generate distribution value without the deduction being too severely compressed.
- Two Life CRUTs consistently outperform Single Life CRUTs at matched ages, despite the smaller deduction — the extended horizon benefit dominates the deduction compression cost for most age combinations.
- Longevity × return interaction is meaningful: at high μ, longer life hurts the CRUT relatively (benchmark compounds harder); at low μ, longer life helps more (turnover drag accumulates over more years).

---

## Notebook 04 — Payout Rate and §7520 Rate
**File:** `notebook_04_payout_7520_2026-02-22.ipynb`

### What Was Examined
Two parameters that determine the CRUT's internal economics at inception: the payout rate (practitioner's primary design choice, ranked #4 OAT) and the §7520 rate (ranked #12, essentially zero influence).

### Why
The payout rate is the only structural parameter the practitioner controls — unlike expected return or longevity, it is chosen deliberately and fixed irrevocably at signing. Getting it right matters. The §7520 rate section addresses and corrects a persistent misconception: many practitioners believe high §7520 rates are unambiguously favorable for CRUTs, but this overstates the rate's influence.

### How It Was Done
- **Payout rate sweep:** 5.0%–9.9% (the corrected engine moves the 10% test failure boundary from 7.9% to 9.9% for Two Life 63/65 at 5% §7520). Non-monotonic win probability curve identified.
- **Payout rate decomposition:** Deduction, PV tax benefit, and PV distributions tracked separately as functions of payout rate.
- **Payout rate × basis and age heatmaps:** Optimal payout rate as a function of donor age and asset basis.
- **§7520 sweep:** Rate varied from 1.2% to 8.2% with pv_rate held fixed at 5%. This is the correct design: per Gottlieb (2025) §4.1 and §4.4, the §7520 rate operates through exactly one channel — the F-Factor in `compute_deduction()`. It is not a discount rate for simulation cash flows. The prior incorrect design varied pv_rate alongside rate_7520, manufacturing a spurious "cancellation" narrative.

### Key Conceptual Correction
The §7520 rate's near-zero OAT sensitivity is explained by two factors working together, not by a cancellation between two channels:

1. **F-Factor insensitivity:** The F-Factor changes modestly as §7520 moves across its realistic range (end-of-period, quarterly payments), producing modest changes in the adjusted payout rate u, remainder factor R, and deduction.
2. **Deduction is ~18% of total CRUT value:** A modest change to a component representing 18% of total value produces near-zero win probability impact.

### Outcome
- Non-monotonic payout rate relationship confirmed: interior optimum exists within the 10% test constraint.
- Optimal payout rate depends on starting age and basis; Figure 4 provides the planning surface.
- §7520 sweep: win probability range across the full historical rate range is less than 1 pp at all turnover levels — confirming #12 rank.
- Planning implication: practitioners need not time CRUT formation around the §7520 rate. The deduction does increase with the rate, but the effect on overall CRUT attractiveness is small.

---

## Notebook 05 — State Tax Sweep
**File:** `notebook_05_state_tax_2026-02-22.ipynb`

### What Was Examined
How CRUT win probability varies across the full spectrum of U.S. state income tax environments, from no-tax states (TX, FL, NV) to high-tax states (CA 13.3%, OR 9.9%, MN 9.85%), including states with differentiated capital gains rates (Hawaii).

### Why
The manuscript baseline uses California rates, appropriate for a California practice but not for the Journal of Financial Planning's national readership. State taxes affect CRUT economics through three distinct channels, and the net direction is not obvious a priori.

### How It Was Done
- **Three-channel framework:**
  - Ch1 (benchmark drag): higher state CG rate → more annual drag on benchmark → favors CRUT
  - Ch2 (distribution tax): higher state ordinary rate → distributions worth less → disfavors CRUT
  - Ch3 (deduction value): higher state ordinary rate → deduction saves more tax → favors CRUT (subject to OBBBA federal cap at 35%)
- State rate swept 0%–13.3% at four turnover levels.
- Named state bar chart: 11 representative states at two turnover levels.
- State rate × basis heatmap.
- Differentiated CG rate analysis (states where ordinary ≠ CG rate).
- National practice planning table for multi-state practitioners.
- `state_cg_rate` parameter added to `ScenarioParams` for states with preferential CG treatment.

### Outcome
- Net effect is monotonically positive: higher state taxes favor the CRUT across all turnover levels. Channels 1 and 3 together outweigh Channel 2.
- No-tax state (0%): 31.2% win probability at 20% turnover.
- California 9.3%: 41.6% win probability at 20% turnover.
- The ~10 pp gap between no-tax and high-tax states is clinically meaningful for national practice planning.
- Differentiated CG rates (Hawaii): the lower state CG rate makes the benchmark slightly more competitive, modestly reducing CRUT win probability relative to a state with the same ordinary rate applied to capital gains.

---

## Notebook 06 — Return Model Robustness
**File:** `notebook_06_robustness_2026-02-22.ipynb`

### What Was Examined
Whether the core findings of the analysis (win probability levels, parameter rankings, planning conclusions) survive when the baseline assumptions about the return-generating process are relaxed. Six robustness checks performed.

### Why
Every Monte Carlo study rests on assumptions. Reviewers and practitioners will ask: What if returns are not log-normal? What if the client holds a concentrated position with high volatility? What if a bear market hits in year one? The series is only as credible as its robustness to these questions.

### How It Was Done

**Section 1 — Path Count Convergence**
- Win probability and 95% bootstrap CI width plotted against n_paths from 200 to 10,000.
- Confirms that 10,000 paths is both necessary and sufficient: CI width falls below 2 pp well before 10,000, but meaningful noise remains at lower counts.

**Section 2 — Volatility Sweep**
- σ swept from 6% to 32% at four turnover levels.
- Addresses concentrated position holders (single stocks, closely held interests, real estate) who commonly carry σ = 20–35%.

**Section 3 — Return × Volatility Joint Surface**
- 2D heatmap of win probability across (μ, σ) combinations.
- Iso-Sharpe ratio contours overlaid to reveal whether the Sharpe ratio or the individual (μ, σ) components drive CRUT performance.

**Section 4 — Student-t Distribution (Primary Robustness Check)**
- Log-normal replaced with scaled Student-t (ν = 5 df) matching identical μ and σ.
- ν = 5 produces excess kurtosis ≈ 6, consistent with observed equity tail behavior.
- Comparison isolates the distributional shape effect independent of location and scale.

**Section 5 — Sequence of Returns Risk**
- 10,000 paths sorted by average return in first third of horizon (years 1–8 of 25).
- Split into quartiles (Q1 early bad through Q4 early good).
- Win probability computed within each quartile to test whether CRUT is more SORR-exposed than the benchmark.

**Section 6 — Bear Market Stress Test**
- Year-1 return multiplied by a shock factor swept from −40% to +20%.
- Tests CRUT formation immediately before a market downturn — a common planning scenario for clients contributing assets near market peaks.

### Outcome
- **Path count:** 10,000 confirmed as appropriate. CI width at n=2,000 was ±1.1 pp; at n=10,000 it falls to ±0.5 pp.
- **Volatility:** Higher σ generally hurts the CRUT at high turnover (benchmark drag is proportionally smaller relative to return variance) but the effect is modest. Zero-floor asymmetry provides limited protection.
- **Student-t:** Win probability at baseline shifts slightly (40.4% vs. 41.6% log-normal). Fat tails do not materially change the qualitative conclusions. The zero-floor advantage for the CRUT from fat left tails is partially offset by fat right tails favoring the benchmark's full-corpus compounding.
- **SORR:** CRUT shows meaningful SORR exposure — early bad returns permanently impair the distribution corpus. However, both strategies show SORR sensitivity, and the difference between quartiles is not large enough to change planning conclusions.
- **Bear market stress:** A −30% year-1 shock substantially reduces CRUT attractiveness. Practitioners should consider phased contributions or delayed formation for clients with near-term market risk.

---

## Series-Wide Technical Notes

### Actuarial Engine
All notebooks share a self-contained copy of the corrected engine implementing:
- **Table 2010CM** (prescribed under §7520, effective June 1, 2023) with complete lx values from age 0 to 110.
- **F-Factor** per Reg. §1.664-4(e)(6)(ii) (Equation 1 of Gottlieb 2025): `F = (1/p) × i × v^(1+d/12) × (1+i)^(1/p) / ((1+i)^(1/p) − 1)`.
- **Single-life remainder** via Treasury Figure 1 summation (Equation 4).
- **Two-life remainder** via joint last-survivor summation (Equations 5–6).
- **OBBBA cap:** deduction tax benefit computed at `min(fed_ordinary, 0.35) + state_rate`.

### Benchmark
Hold-liquidation: benchmark holds the contributed asset, pays annual capital gains tax on realized turnover, and liquidates at the end of the simulation horizon. This is the primary benchmark throughout. NB02 also examines hold-to-death and liq-reinvest alternatives.

### §7520 Rate Architecture
The §7520 rate enters the simulation exclusively through `compute_deduction()` via the F-Factor. It is not a discount rate for simulation cash flows (`pv_rate` is an independent donor preference parameter). This distinction is critical: the rate's near-zero OAT sensitivity reflects F-Factor insensitivity and the deduction's small share of total CRUT value — not a cancellation between two channels. See Gottlieb (2025) §4.1 and §4.4.

### Paired-Path Design
Both CRUT and benchmark use identical return paths in each simulation. This eliminates sampling noise from the win probability comparison and is conservative for the CRUT: it excludes the concentration risk scenario where the contributed asset performs differently from a diversified benchmark — the most favorable CRUT scenario.

---

*Series prepared February 22, 2026. All notebooks cleared and ready for final re-run on Google Colab.*
