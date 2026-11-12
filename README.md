# Consumer Demand Alignment: Profile Matching with Cosine Similarity

A Python project that measures how closely local consumer demand profiles match a set of global reference clusters, and recommends whether each local profile should keep its current cluster, be re-mapped, or be reviewed.

**Note**: This repository uses synthetic data only.


## Business Problem

Global consumer teams define reference clusters for consumption occasions. Local markets have their own demand spaces, and each must be mapped to the closest global cluster. 

Question: Which global cluster best matches each local demand profile, and which current mappings should be reviewed?

## Approach

Each demand space is described as a profile across four dimensions:

| Dimension | What it captures |
|---|---|
| Demand need | The consumer need the occasion serves |
| When | Time of day the occasion occurs |
| Who | Age group of the consumer |
| Where | Location of consumption |

The pipeline:

1. Harmonize inputs. Combine source files with different structures, standardize category labels, and convert numeric fields stored as text.
2. Build profiles. Convert each demand space into a set of values across all four dimensions, using both share-based and index-based measures.
3. Scale index values. Clip extreme values to percentile-based limits and scale them to a common range so they can be compared with share values.
4. Score alignment. For each local profile and each global cluster, compute cosine similarity within each dimension, then take a weighted average across dimensions.
5. Recommend action. Apply rules based on the strength of the current match, the best alternative, and the gap between them.
6. Export results. Write a summary workbook with recommendations, the full similarity matrix, and a long-form breakdown by dimension.

## Example Output

The table below is generated from synthetic data and shows the kind of output the pipeline produces. Values are illustrative.

| Local Demand Space | Current Cluster | Current Score | Best Match | Best Score | Gap | Recommendation |
|---|---|---|---|---|---|---|
| Space A | Cluster 1 | 0.91 | Cluster 1 | 0.91 | 0.00 | Keep: strong alignment |
| Space B | Cluster 2 | 0.72 | Cluster 4 | 0.88 | 0.16 | Re-map |
| Space C | Cluster 3 | 0.84 | Cluster 3 | 0.84 | 0.00 | Keep: moderate alignment |
| Space D | Cluster 5 | 0.78 | Cluster 6 | 0.81 | 0.03 | Close: for review |


## Key Techniques

- **Cosine similarity** to compare profile shape within each dimension
- **Weighted scoring** to combine dimension-level results into one alignment score
- **Percentile clipping and min-max scaling** to make index values comparable with share values
- **Rule-based recommendations** that flag strong matches, weak matches, and close alternatives
- **pandas** for reshaping long and wide tables, grouping, and pivoting
