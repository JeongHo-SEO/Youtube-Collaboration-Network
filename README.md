# 유튜브 협업 네트워크에서의 유튜버 관계 예측 및 군집 분석

**Predicting YouTuber Relationships and Community Structures on YouTube Collaboration Networks**

> [!NOTE]
> DGIST UGRP · Social Network Analysis · Link Prediction · Community Detection

## Resources

- **[View Interactive Network](https://jeongho-seo.github.io/Youtube-Collaboration-Network/Graph/network_260429.html)**

  Drag nodes and zoom to explore collaboration patterns.

- **[Download Youtuber-Game-DB](https://docs.google.com/spreadsheets/d/1hg9jO6jCVfX8tV0xALZX9_zikVfKKxj8z_1g7i9ljas/edit?usp=sharing)**

  A database of gaming YouTubers and their collaborations, manually compiled by the team.

## Problem

**Who might collaborate next, and which communities emerge from existing collaborations?**

We initially aimed to study creators across all categories. YouTube API and crawling limitations led us to narrow the scope to Korean gaming creators and collect the data manually.

The records exposed a modeling gap: when host A features guests B, C, and D, recording only `A→B`, `A→C`, and `A→D` **misses the relationships among guests**.

## Methodology

| Task | Approach |
| --- | --- |
| Graph Modeling | 1. Group collaboration records by video.<br>2. Add discounted links between guests.<br>3. Sum weights across repeated collaborations. |
| Network Analysis & Visualization | 1. Use PageRank for global importance and PPR for relevance to selected channels.<br>2. Explore connections through interactive network views. |
| **Link Prediction** | 1. Explore **PPR, Common Neighbors, and Adamic–Adar**.<br>2. Compare embedding-based rankings with **Node2Vec** and weighted co-occurrence.<br>3. Test **GCN** for link prediction. |
| **Community Detection** | 1. Apply **Louvain** to the directed, weighted graph.<br>2. Compare the resulting communities with *known creator crews*. |

### Edge Weighting

| Connection | Weight per video |
| --- | --- |
| Host → guest | `1.0` |
| Guest → guest (each direction) | `0.5 × γ` |

- **Setting:** `γ = 0.5` → `0.25` per directed guest link.
- **Interpretation:** Modeling weights, not measured social closeness.

## Results

Selected experimental results from the project.

### 1. Link Prediction

| Experiment | Key result |
| --- | --- |
| **PPR evaluation** | **ROC-AUC: 0.872**.<br>Unweighted, undirected graph; 899 training edges; 225 held-out edges + 225 negative test pairs. |
| **Common Neighbors / Adamic–Adar** | Hub channel 후추 appeared repeatedly among top candidates.<br>AA also surfaced pairs absent from the CN top list, reflecting its degree-adjusted weighting. |
| **GCN exploration** | Tested identity-matrix node features with a dot-product + sigmoid decoder.<br>Compared graph inputs with and without duplicate collaboration records. |

**Node2Vec scoring comparison:** the same embeddings produced different top-ranked pairs depending on the scoring rule.

| Scoring rule | Top-ranked pair | Score |
| --- | --- | --- |
| Cosine similarity ↑ | 우융 ↔ 조밈 | 0.9476 |
| Dot product ↑ | 감스트GAMST ↔ 아오니 | 17.9911 |
| L2 distance ↓ | 우융 ↔ 조밈 | 1.0162 |

Cosine similarity and L2 shared many leading candidates, while dot product favored a different set. These are ranking scores on different scales, not collaboration probabilities or held-out accuracy.

### 2. Community Detection

The directed, weighted graph contained **186 channels and 3,390 aggregated edges**. Louvain identified **11 communities** with `resolution=1.0`.

| Community | Selected members | Interpretation |
| --- | --- | --- |
| **2** · 8 channels | 양띵, 다주, 서넹, 루태, 삼식 | Overlap with the 양띵 crew |
| **4** · 9 channels | 악어, 너불, 핑맨, 멋사 | Overlap with the 악어 crew |
| **6** · 2 channels | 태경 TV, 쁘허 | A recognizable collaboration pair |

Other larger groups did not map neatly to known crews. These groups required further interpretation and quantitative evaluation; familiar groupings alone did not establish clustering accuracy.

### 3. Network Analysis & Visualization

Weighted PageRank shifted rankings toward repeated collaboration partners. Comparing weighted and unweighted graphs highlighted the difference between **collaboration frequency** and **collaboration breadth**. Interactive views helped inspect these connections.

*Results are drawn from recorded experiments. The PPR AUC and Node2Vec candidate scores use different evaluation setups.*

## Findings

- **Collaboration breadth and frequency reveal different aspects of a channel.** Adding repeated-collaboration weights changed which channels ranked highly; frequent partners and broadly connected creators were not always the same.
- **The scoring rule changes the recommendation.** Different rankings from the same embeddings show that choosing a similarity measure is part of defining what makes a promising collaboration candidate.
- **Known crews explain only part of the community structure.** Some detected groups matched familiar crews, while others required further interpretation. Crew overlap alone was insufficient to judge community quality.

These observations are limited to the gaming dataset and the tested settings. Further evaluation should hold other conditions fixed when comparing graph representations and models.

## My Contribution

I proposed and implemented the discounted guest-link model, analyzed PageRank/PPR, and explored Louvain communities and Node2Vec scoring. I also contributed to discussions on evaluation design.

**This repository publishes selected code from my work, covering only part of my contribution.**

- [Data conversion](Tasks/260429/convert_data.ipynb)
- [Weighted graph](Tasks/260429/PR-PPR.ipynb)
- [PR & PPR](Tasks/260422/PR-PPR.ipynb)
- [Louvain & Node2Vec](Tasks/260506/simple_model.ipynb)

