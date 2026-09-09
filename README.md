# CS:GO Player Archetype Clustering

## What this is

I built this project to answer a simple question: can you figure out a CS:GO player's natural playstyle just by looking at their match stats, without anyone telling you what "type" of player they are?

This is unsupervised learning, which means there are no labels to guide the algorithm. Nobody tagged players as "aggressive" or "support" beforehand. The whole point is letting the data speak for itself and seeing what groupings actually emerge.

I used two different clustering algorithms, K-Means and Hierarchical Clustering, and compared them side by side instead of just picking one and moving on. Most beginner projects stop at applying a single algorithm. I wanted to actually test whether different methods agree with each other, and understand why they might not.

## The data

I used the Brazilian CS:GO Platform Dataset from Gamers Club, available on Kaggle. The raw file had 184,152 rows, but each row represented a single match performance, not a player's overall behavior. A player having one lucky game with 30 kills doesn't tell you their actual playstyle, so I grouped every match by player ID and calculated their average stats across all their games.

I also filtered out players with fewer than 10 matches. Averaging someone's stats from just 2 or 3 games isn't reliable, there's too much variance from luck alone. After filtering, I was left with 1,805 players, each with a stable, trustworthy average of their behavior.

## Building the features

Raw numbers like "18 kills per game" don't mean much on their own since they depend heavily on how long a match went or how skilled the lobby was. So instead of feeding raw stats into the clustering algorithm, I engineered 6 ratio-based features meant to actually capture playstyle:

**kda_ratio** measures overall effectiveness, combining kills and assists against deaths.

**headshot_rate** captures aim skill, showing what fraction of a player's kills come from headshots.

**win_rate** is straightforward, how often their team wins.

**entry_aggression** looks at first kills per round, showing whether a player tends to push in first or hang back.

**support_score** measures flash assists relative to kills, picking up on players who help teammates more than they frag themselves.

**objective_score** tracks bomb plants and defuses per round, separating objective-focused players from pure kill farmers.

Before clustering, I scaled all 6 features using StandardScaler. This matters because kda_ratio ranges up to about 2, while objective_score tops out around 0.15. Without scaling, the algorithm would have treated kda_ratio as far more important simply because its numbers are bigger, not because it's actually more meaningful.

## Finding the right number of clusters

Rather than guessing how many player groups to look for, I tested K values from 2 to 10 using Silhouette Score, which measures how tight and well-separated each grouping is. The scores peaked early and declined as K increased, and I settled on K=4 as a reasonable balance between meaningful groupings and not over-splitting the data into artificial distinctions.

## Running both algorithms

**K-Means** grouped players directly into 4 clusters. It scored a Silhouette Score of 0.147.

**Hierarchical Clustering** built a full tree structure showing how players relate at different similarity levels, visualized as a dendrogram, then I cut the tree to also produce 4 clusters for a fair comparison. It scored 0.083, noticeably lower than K-Means.

The PCA visualization made the difference clear. K-Means produced clean, distinct regions with fairly sharp boundaries between clusters. Hierarchical Clustering's regions overlapped much more heavily, especially toward the middle of the plot.

## Where the two algorithms agreed and disagreed

I built a cross-tabulation to check how much the two methods actually agreed on grouping the same players together, not just producing similarly sized clusters. The result was genuinely interesting. On the strongest-performing player group, the two algorithms agreed 85% of the time, meaning both methods independently discovered the same distinct group of top performers. But on the middle-performing players, agreement dropped significantly, with players scattered across different clusters depending on which algorithm was used.

This tells you something real. Elite and weak players are easy for any algorithm to separate out. Average players exist on more of a blended spectrum, and small differences in how each algorithm measures similarity can push them into different groups.

## Choosing K-Means

Given the higher Silhouette Score and the cleaner visual separation, I went with K-Means as the primary result for interpretation. I kept the Hierarchical dendrogram in the project too, since it independently validates that the top-performer cluster is a real, robust pattern and not an artifact of one specific algorithm.

## The four archetypes

**Dominant Fragger** stood out immediately, with the highest KDA, win rate, and entry aggression of any group. This was also the cluster both algorithms agreed on most strongly.

**Balanced Aggressor** performed solidly across the board without any single standout trait.

**Support/Utility Player** was the one I almost mislabeled. Looking only at average KDA, this group looked like a plain "medium" cluster with nothing special going on. It was only after breaking the data down feature by feature using box plots that I noticed this group actually had the highest support_score of all four clusters. These aren't just average performers, they're playing a genuinely different role centered on helping teammates rather than racking up kills themselves. Catching this changed how I understood the whole cluster.

**Passive/Low Impact** scored lowest across most metrics and appeared least involved in fights overall.

## Being honest about the results

The Silhouette Scores here were moderate at best, nowhere close to what you'd call a clean, sharply separated clustering. I think that's an accurate reflection of the data rather than a flaw in the approach. Casual and ranked CS:GO players don't stick to one fixed role match after match the way professional players with defined roles might. Someone might play aggressively one game and passively the next depending on their team composition or the situation. The clusters found here represent general tendencies in how players typically behave, not rigid categories.

## What I'd do differently or add next

Bringing in map-specific performance or weapon preference data could sharpen the separation between groups. I'd also want to try DBSCAN or Gaussian Mixture Models, since both might handle this kind of blended, overlapping behavioral data better than K-Means, which assumes roughly spherical, evenly-sized clusters. Comparing this casual player dataset against professional or semi-professional player data could also be interesting, since pros likely stick to more clearly defined roles, which might produce sharper, more distinct clusters.

## Project structure

gaming-player-clustering/
├── myProject.ipynb full analysis notebook with code and narrative
├── data/
│ └── processed/
│ └── clustering_results.csv final player-level results with cluster assignments
├── outputs/ saved visualizations (dendrogram, PCA plots, heatmaps, comparisons)
├── requirements.txt project dependencies
└── README.md this file


## Tools used

Python, pandas, numpy, scikit-learn, scipy, matplotlib, seaborn, all run in a Jupyter notebook inside VS Code.

For the full analysis with all code and visualizations, open `myProject.ipynb`.