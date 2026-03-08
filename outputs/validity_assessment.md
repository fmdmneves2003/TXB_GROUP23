# Validity Assessment — CTA Button Experiment (TXB_23)

## Experiment Summary

- **Page:** Events page
- **Intervention:** Redesigned CTA button (treatment) vs. original button (control)
- **Sample:** 1,163 visitors — 349 control (`pre`), 814 treatment
- **Duration:** Feb 22 – Mar 6, 2026
- **Primary KPI:** `kpi_y` (CTA click rate) — significant negative effect (p = 0.044)
- **Secondary KPI:** `kpi_x` (enrollment-readiness score) — negative direction, not significant (p = 0.105)

---

## Threats to Internal Validity

Internal validity concerns whether the estimated treatment effect is a true causal effect within the experiment, or whether something in the experimental design or execution could have distorted the result.

### 1. Sample Ratio Mismatch (SRM)

The observed allocation is approximately 70% treatment / 30% control. The SRM investigation (`03_SRM_investigation.ipynb`) found:

- Under a **50/50** intended split: severe SRM (p ~ 0). This would indicate a fundamental failure in the randomisation mechanism.
- Under a **70/30** intended split: no SRM detected (p = 0.995).

The analysis proceeds under the assumption that the 70/30 split was intentional. **This assumption must be verified with the experiment owner.** If the intended split was 50/50, then randomisation integrity is compromised: the treatment and control groups may differ systematically in ways unrelated to the button change, and all causal claims become unreliable.

### 2. SUTVA (Stable Unit Treatment Value Assumption)

SUTVA requires that one visitor's treatment assignment does not affect another visitor's outcome. This could be violated if:

- Visitors share devices and see different button versions across sessions.
- Word-of-mouth effects exist (e.g., one visitor tells another about the page).
- There is network interference between visitors.

There is no direct way to test SUTVA with the available data. In a web-based experiment on an events page, interference between visitors is unlikely but cannot be ruled out entirely.

### 3. Lack of Statistical Power

The power analysis (`02_power_analysis.ipynb`) revealed that the experiment is underpowered for both KPIs:

| KPI | Required n per group | Actual control | Actual treatment |
|-----|---------------------|----------------|-----------------|
| `kpi_y` (detect +3.1 pp) | ~1,308 | 349 | 814 |
| `kpi_x` (detect +4 points) | ~776 | 349 | 814 |

**Implications:**

- The experiment has a high risk of **false negatives** (Type II error) — it may fail to detect a real positive effect.
- The significant negative result on `kpi_y` (p = 0.044) is informative despite low power: finding significance when underpowered means the true effect is likely real rather than a false positive.
- The non-significant result on `kpi_x` (p = 0.105) is harder to interpret: it could reflect either no real effect or a real negative effect that the experiment lacks the power to confirm.

### 4. Multiple Hypothesis Testing

Five metrics are tested simultaneously without a correction procedure (e.g., Bonferroni, Benjamini-Hochberg). Under Bonferroni at 5 tests, the adjusted significance threshold would be 0.05 / 5 = 0.01.

| Metric | p-value | Significant at 5%? | Survives Bonferroni (1%)? |
|--------|---------|--------------------|-----------------------------|
| `kpi_y` | 0.044 | Yes | No (borderline) |
| `kpi_x` | 0.105 | No | No |
| `exit_rate` | 0.0003 | Yes | Yes |
| `scroll_depth_pct` | 0.029 | Yes | No |
| `page_load_time_ms` | 0.010 | Yes | No (borderline) |

The primary KPI result (p = 0.044) does not survive a strict Bonferroni correction, though this is arguably over-conservative given that the primary hypothesis was pre-specified. The exit rate result (p = 0.0003) is robust to any reasonable correction.

---

## Threats to External Validity

External validity concerns whether the experimental results generalise beyond the specific context of the experiment — to other users, other time periods, or other settings.

### 1. Novelty Effects

The experiment ran for approximately two weeks. Visitors who encountered an unfamiliar button design may have reacted negatively simply because it was new and different from what they expected. If so:

- The negative treatment effect may be **transient** — it could attenuate as users become accustomed to the new design.
- A longer test or a repeated test after an adjustment period would help distinguish a genuine usability problem from a short-term novelty reaction.

**We have not tested for a declining treatment effect over time**, which would be the signature of a novelty effect (the treatment effect would be large initially and shrink in later days).

### 2. Primacy Effects

The opposite of novelty effects: users may resist any change out of habit and familiarity with the original design. If the new button is objectively better but users need time to adapt:

- The short two-week window could produce a **misleadingly negative** result.
- A solution would be to wait longer or re-run the experiment after the initial adaptation period.

### 3. Heterogeneous Treatment Effects

The Average Treatment Effect (ATE) averages across all visitors. It is possible that:

- The new button works **well** for some segments (e.g., mobile users, first-time visitors, visitors from specific traffic sources) but **poorly** for others.
- The overall negative ATE masks a positive effect in a specific subgroup.

No segmented analysis was performed in this experiment. Useful segments to investigate would include:

- Device type (mobile vs. desktop)
- Time of day / day of week
- New vs. returning visitors
- Traffic source

Without this analysis, we cannot know whether the treatment has heterogeneous effects.

### 4. Selection Bias

The results apply strictly to visitors who arrived at the Events page during the test window (Feb 22 – Mar 6, 2026). Several factors could limit generalisability:

- **Seasonal patterns:** Visitor behaviour may differ at other times of year (e.g., event registration cycles, academic calendar).
- **Marketing campaigns:** If specific campaigns were running during the test period, the visitor composition may not be representative of typical traffic.
- **Platform population:** Visitors to this specific Events page may not represent users of the broader platform.

### 5. Simpson's Paradox

With a 70/30 split and day-level variation in both traffic volume and metric levels, overall averages could in principle mask day-level patterns. For example:

- If the treatment group was disproportionately larger on days when baseline behaviour was worse, the aggregate result could appear negative even if the treatment had no effect (or a positive effect) on each individual day.
- The **date-fixed-effects robustness check** in the regression analysis partially addresses this by controlling for day-level variation. The `kpi_y` result remains significant (p = 0.038) after adding date fixed effects, which provides some reassurance.
- However, a full day-by-day breakdown of the treatment effect was not conducted.

---

## Summary Table

| Threat | Category | Severity | Addressed? |
|--------|----------|----------|------------|
| SRM (if 50/50 intended) | Internal | **High** | Partially — passes for 70/30, unconfirmed |
| SUTVA violation | Internal | Low | Not testable |
| Lack of power | Internal | **High** | Acknowledged; limits kpi_x interpretation |
| Multiple testing | Internal | Medium | Not corrected; primary KPI borderline |
| Novelty effects | External | Medium | Not tested (no time-trend analysis) |
| Primacy effects | External | Medium | Not tested |
| Heterogeneous effects | External | Medium | Not tested (no segmentation) |
| Selection bias | External | Low–Medium | Inherent to the test window |
| Simpson's paradox | External | Low | Partially addressed by date FE |

---

## Key Takeaways

1. The most critical validity threat is the **unconfirmed allocation ratio**. If the intended split was 50/50 rather than 70/30, internal validity is fundamentally compromised.

2. The experiment is **underpowered**, which limits the ability to detect positive effects and makes the non-significant result on `kpi_x` inconclusive. However, the significant negative finding on `kpi_y` remains informative.

3. **Novelty and primacy effects** are plausible given the short test duration. A longer or repeated experiment would help rule these out.

4. **No segmented analysis** was performed. The overall negative ATE could mask heterogeneous effects across visitor subgroups.

5. Despite these limitations, the **convergence of negative signals** across multiple metrics (kpi_y, kpi_x, exit_rate, scroll_depth) and across multiple methods (difference-in-means, OLS, date-FE OLS) strengthens the overall conclusion that the treatment button performed worse than the original.
