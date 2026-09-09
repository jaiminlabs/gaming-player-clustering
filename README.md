# CS:GO Player Archetype Clustering

Using unsupervised learning to discover natural player playstyles from raw CS:GO match statistics, no labels, just letting the data group itself.

## The approach

Started with 184,152 individual match rows from the Gamers Club Brazilian CS:GO dataset. Since each row was just one match, I aggregated stats by player and kept only players with 10+ matches for reliable averages, leaving 1,805 players.

Built 6 engineered features to capture playstyle rather than raw counts: kda_ratio, headshot_rate, win_rate, entry_aggression, support_score, and objective_score. Scaled everything before clustering so no single feature dominated just because its numbers were bigger.

## Comparing two algorithms

Ran both K-Means and Hierarchical Clustering with K=4 (chosen via Silhouette Score testing). K-Means scored higher (0.147 vs 0.083) and showed cleaner separation in PCA plots.

Cross-checking the two algorithms revealed something interesting: they agreed 85% of the time on the top-performing player cluster, but disagreed much more on mid-performing players. Elite players are easy to separate. Average players blend together more.

Went with K-Means as the primary model, kept the Hierarchical dendrogram as supporting evidence that the top cluster is genuinely robust.

## What I found

Four archetypes emerged: **Dominant Fragger** (highest KDA, win rate, aggression), **Balanced Aggressor** (solid all-around), **Support/Utility Player**, and **Passive/Low Impact**.

The Support/Utility cluster was the interesting catch, initial average-based naming made it look like a plain "medium" group, but breaking it down feature by feature showed it actually had the highest support_score of all clusters. It's a distinct role, not just average performance.

## Honest limitations

Silhouette scores were moderate, not high. This likely reflects reality, casual players don't stick to fixed roles the way pros might. The clusters represent general tendencies, not rigid categories.

## What I'd try next

Adding map-specific stats, testing DBSCAN or Gaussian Mixture Models for this kind of blended data, or comparing against pro-level player data to see if roles come out sharper.

## Structure

myProject.ipynb full analysis, code, and visualizations
data/processed/ final clustering results (CSV)
outputs/ saved plots (dendrogram, PCA, comparisons)


Built with Python, pandas, scikit-learn, scipy, matplotlib, seaborn in Jupyter.