# Content Refresh Opportunity Scoring

## Abstract

This study develops a machine-learning ranking approach for prioritizing content pages for editorial review. Using February 2026 search-performance signals, the model estimates which pages subsequently went dark during the March 2026 outcome window. A Random Forest model was evaluated against a rule-based baseline using a client-grouped holdout split. On the held-out test set, the model achieved Precision@20 of 0.20 versus 0.05 for the baseline, and Precision@50 of 0.24 versus 0.02 for the baseline. The results suggest that the learned ranking can provide useful decision support for prioritizing content review, while not establishing causal effects of content refreshes or search-engine behavior.

## 1. Introduction / Problem Statement

Content teams cannot manually review every page with equal priority. The decision problem is to identify which pages should receive editorial attention first using observable search-performance signals.

This work focuses on refresh opportunity scoring. The objective is to rank content pages so that editors and SEO teams can review the highest-priority opportunities first. The system is intended as a decision-support layer rather than an autonomous refresh decision-maker.

## 2. Data

The analysis uses the FlyRank internship warehouse dataset.

The feature window is February 2026 and the outcome window is March 2026.

The unit of analysis is one content item for one client over the defined feature and outcome windows.

The February universe was restricted to pages with:
- at least 100 GSC impressions,
- at least 3 GSC clicks,
- published content status,
- content created on or before February 28, 2026,
- measurable GSC data.

Pages without measured March GSC data were excluded from the final evaluation.

The final analysis dataset contained 29,353 page-level observations, with 1,159 positive outcomes, corresponding to a positive rate of 3.95%.

## 3. Methodology

### Features

The model uses three February 2026 search-performance features:

- GSC impressions
- GSC average position
- CTR, calculated as total GSC clicks divided by total GSC impressions

CTR was calculated from clicks and impressions because a direct CTR field was not used from the raw warehouse table.

### Outcome Label

The outcome label is `went_dark`.

A page is labelled positive when it records zero GSC clicks during the measured March 2026 outcome window.

### Baseline

The rule-based baseline prioritizes pages using February visibility and search-position signals.

The baseline assigns higher priority to pages with at least 1,000 impressions and an average position worse than 10. Lower-volume pages with poor position receive a lower score.

### Model

A Random Forest classifier was trained using:
- 300 trees
- maximum depth of 8
- minimum samples per leaf of 10
- balanced class weights
- random seed of 42

### Validation Design

The dataset was split using a client-grouped 80/20 holdout. This prevents pages belonging to the same client from being distributed across both training and test sets.

The model was evaluated on the same held-out test set used for baseline comparison.

### Leakage Checks

Only February feature-window signals were used as model inputs. March outcome information was used only to construct the target label and was not used as a model feature.

## 4. Results

The Random Forest model outperformed the rule-based baseline on the same client-grouped test split.

| Metric | Baseline | Random Forest |
|---|---:|---:|
| Precision@20 | 0.05 | 0.20 |
| Precision@50 | 0.02 | 0.24 |

The Random Forest achieved a ROC-AUC of 0.7966 and an Average Precision of 0.1417 on the held-out test set.

The results indicate that the learned ranking was more effective than the simple baseline at concentrating positive outcomes near the top of the review queue.

These results should be interpreted as measured predictive performance and decision-support evidence, not as proof that refreshing a page will improve its future performance.

## 5. Limitations

- The outcome label is based on observed March GSC behavior and does not establish why a page went dark.
- The model does not prove that refreshing a page will improve traffic, rankings, or conversions.
- The feature set is intentionally limited to observable February search-performance signals.
- Performance may vary across future releases, clients, and content types.
- GSC measurements can be affected by data availability and tracking limitations.
- The model should support editorial prioritization rather than replace human review.
- The evaluation covers a specific February-to-March 2026 time window and should be re-evaluated on future periods.

## 6. Ranked Recommendations

1. **Review the highest-scoring pages first.** Use the Random Forest ranking to create a focused editorial review queue.

2. **Prioritize pages with meaningful historical visibility.** Pages with measurable search exposure and weaker positioning can represent valuable review opportunities.

3. **Validate content before refreshing.** Editors should assess freshness, relevance, search intent, title quality, and content quality before taking action.

4. **Track post-refresh outcomes.** Compare future observed performance against the pre-refresh baseline while avoiding causal claims from observational changes.

5. **Re-evaluate the model over time.** As additional feature and outcome windows become available, retrain and validate the ranking approach.

## 7. Reproducibility

The analysis is implemented in the capstone notebook in the repository.

The notebook contains the feature construction, outcome definition, grouped validation split, Random Forest training, baseline comparison, evaluation metrics, and charts.

## 8. Public-Safe Framing

This work uses anonymized data and does not expose client names, domains, URLs, private search queries, credentials, or raw client exports.

The results describe observed and measured relationships in the internship dataset. They do not claim to explain Google's ranking algorithm or demonstrate causal refresh impact.

## 9. Acknowledgments & Data Credit

Built on the FlyRank ML Internship dataset.

[FlyRank](https://flyrank.ai)
