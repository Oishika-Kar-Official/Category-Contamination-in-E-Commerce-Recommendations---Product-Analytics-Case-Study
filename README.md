# Meesho Recommendation Engine — Category Contamination Audit

**Built by:** Oishika Kar | 2026

---

## Overview

While browsing the Meesho app, I clicked on the **Lights** category and received diapers, kitchen items, and other completely unrelated products in the top results. Other categories like Kitchen & Dining returned correctly scoped results — pointing to a **category-specific failure**, not a global pipeline breakdown.

This project diagnoses the root cause, quantifies the contamination, proposes a fix, and visualises the before vs. after impact.

---

## Problem Statement

> Clicking on *Lights & Lamps* in Meesho returned irrelevant products (diapers, personal care items) in the top 20 results. Kitchen & Dining, by contrast, showed correctly scoped results (aprons, cookware, etc.).

This asymmetry suggests the issue is tied to **category keyword ambiguity** — "Lights" overlaps with Baby Care (nightlights), Home Decor (fairy lights), and Kitchen (under-cabinet LEDs). Sellers exploit this by tagging listings with high-traffic keywords outside their true category.

---

## Repository Structure
```
meesho-recommendation-audit/
│
├── Meesho_Recommendation_Audit.ipynb   # Main notebook (all 4 parts)
├── meesho_interactive_dashboard.html   # Plotly interactive dashboard
├── meesho_static_dashboard.html        # Styled HTML portfolio page
├── meesho_dashboard.png                # Static Matplotlib dashboard
└── README.md
```

---

## Project Structure

### Part 1 — Mock Catalog Generation
Simulates a Meesho-style product catalog with 75 products across 7 categories. Realistic seller tagging noise is injected with category-specific rates:

| Category | Noise Rate | Reason |
|---|---|---|
| Lights & Lamps | 65% | High keyword ambiguity — overlaps with Baby, Home, Kitchen |
| Home Decor | 35% | Moderate ambiguity |
| Personal Care | 25% | Low-moderate |
| Diapers & Baby Care | 20% | Low |
| Toys & Games | 20% | Low |
| Kitchen & Dining | 15% | Low — category keywords are specific |
| Stationery | 15% | Low |

Noise types modelled:

| Type | Description |
|---|---|
| `paid_placement` | Sponsored listing placed in wrong category for broad reach |
| `broad_tag` | Seller used a high-traffic keyword to boost visibility |
| `bundle_bleed` | Wrong category product bundled/compared in listing title |
| `taxonomy_error` | Seller miscategorised during upload |

### Part 2 — Diagnosis
Simulates Meesho's current retrieval logic — matches products on **primary OR noise tag**, then applies a sponsored boost before any category filtering. Quantifies contamination on a "Lights & Lamps" query.

### Part 3 — Fixed Pipeline
Proposes a re-ranking fix:
1. **Hard category filter first** — only primary tag must match
2. **Quality scoring** — weighted CTR + seller rating + within-category sponsored boost
3. **Personalization capped at 20%** — cannot override category relevance

This preserves Meesho's monetization model (sponsored listings still surface) while eliminating cross-category bleed.

### Part 4 — Metrics Dashboard
Visualises before vs. after across four charts:
- Category contamination rate (current vs. fixed)
- Contamination rate by category (Lights highlighted)
- Root cause breakdown (pie)
- CTR distribution with uplift

---

## Key Results

| Metric | Current Pipeline | Fixed Pipeline |
|---|---|---|
| Contamination Rate (Lights query) | ~12% | 0% |
| Avg CTR — Top 20 Results | Lower | Higher |
| Relevant Results in Top 20 | 15 / 20 | 20 / 20 |

---

## Key Insight

The fix does not remove Meesho's monetization lever. Sponsored listings still get a boost — they just have to earn it within the correct category. This is a **zero-revenue-cost fix** with a direct positive impact on CTR and user satisfaction.

---

## Limitations

1. **Mock catalog** — 75 synthetic products. Real Meesho has millions of listings; contamination patterns at scale may differ.
2. **0% contamination is theoretical** — In production, primary tags also carry seller errors. A realistic fix would bring contamination to ~1–3%, not zero.
3. **Noise rates are estimates** — Based on known marketplace self-tagging behaviour, not measured from Meesho's actual catalog.
4. **Personalization is simulated as random** — Real engines use browsing history, purchase signals, and session context.
5. **CTR is synthetic** — The uplift shown is directionally correct but not a measured business outcome.
6. **Single query tested** — Contamination severity likely varies across categories.
7. **No A/B test data** — Impact on conversion or revenue cannot be validated without live traffic access.

---

## Tech Stack

- Python 3.12
- pandas, numpy
- matplotlib, seaborn
- plotly
- Jupyter Notebook

---

## Author

**Oishika Kar**
Data Analyst | M.S. Quantitative Economics (UCLA)
[LinkedIn](#) | [Portfolio](#)
