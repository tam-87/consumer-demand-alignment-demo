# Method Notes

This file explains the reasoning behind each step. For an overview, see the README.

## Problem

Each local demand space needs to be matched to the global cluster it most matches with. There's no correct label to learn from, so this is a similarity problem. For each local space, the output is a ranked list of candidate clusters and a recommendation.

## Profiles

Each demand space is described across four dimensions:

- **Demand need:** the consumer need the occasion serves
- **When:** time of day
- **Who:** age group
- **Where:** location of consumption

Each dimension is measured two ways:

- **Share:** how consumption splits across categories. Shares add up to 100% within each dimension.
- **Index:** how much each category is over or under the overall average. Values are centered on zero.

Shares show the mix. Index shows what stands out.

## Scaling Index Values

Index values can have extreme outliers, so the pipeline:

1. Caps values at the 5th and 95th percentiles.
2. Scales local and global index values together to a 0-to-1 range, so they're comparable.
3. Scores index values separately from shares, since they measure different things.

## Scoring

For each local space and each global cluster:

1. **Cosine similarity** is calculated within each dimension. It compares shape, not size.
2. **Weighted average** combines the dimension scores into one score from 0 to 1. Time of day and age carry the most weight. Location carries the least. The weights are a judgment call.
3. **Dimension scores are kept** alongside the overall score, so reviewers can see what drives a result.

## Recommendations

Each local space gets one recommendation based on its current cluster, its best match, and the gap between them. The thresholds below are illustrative:

| Situation | Recommendation |
|---|---|
| Best score under 0.70 | Low overall match |
| Current cluster is best and scores 0.90+ | Keep: strong |
| Current cluster is best and scores 0.75 to 0.90 | Keep: moderate |
| Current cluster is best and scores under 0.75 | Keep: weak |
| Another cluster is best by 0.05 or more | Re-map |
| Another cluster is best by under 0.05 | Close: review |

Thresholds are team decisions, not statistical results. They should be tested against past decisions before anyone relies on them.

## Why These Choices

- **Cosine over Euclidean distance:** we care about the shape of the mix, not its overall size.
- **Shares and index scored separately:** keeps each measure easy to interpret.
- **Rules alongside the score:** a single number can hide a close call between the top two clusters, so the rules flag it.

For known limitations, see the README.


Before committing, check that the weighting and threshold descriptions are safe to share. If not, replace the table with one sentence: "Thresholds were set by the team and checked against past decisions."
