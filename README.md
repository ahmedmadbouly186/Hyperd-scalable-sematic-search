# Hyperd Scalable Indexer — IVFFLAT Only

This branch implements a **single-layer IVFFLAT indexer** without LSH.  
It focuses on **incremental insertion**, **dynamic clustering**, and **memory-efficient reindexing**.

> This branch is intended for studying IVFFLAT behavior in isolation and comparing it with the two-layer LSH + IVFFLAT approach.

---

## Overview

The index starts with no predefined clusters and dynamically builds clustering structure as data is inserted.

Unlike traditional IVFFLAT:

- Clusters are formed incrementally
- Reindexing is triggered based on dataset growth
- Disk usage is prioritized over RAM usage

---

## Indexing Strategy

- Start with **empty clusters**
- Initially:
  - Each inserted vector becomes a new cluster centroid
- For subsequent inserts:
  - Each vector is assigned to the nearest centroid using cosine similarity

---

## Reindexing Policy

- Reindexing is triggered when:
  - Dataset size becomes **double** the size at the last indexing stage
- Re-clustering is designed to:
  - Minimize RAM usage
  - Leverage disk-based storage
  - Avoid blocking the system during rebuilds

---

## Retrieval

- Standard IVFFLAT retrieval:
  - Compute similarity to centroids
  - Select nearest clusters (`nprobes`)
  - Perform linear search within selected clusters
  - Return top **k** results

---

## Experimental Results

- Number of clusters: **1000**
- nprobes: **150**
- Vector dimension: **70 (float)**
- Total inserted vectors: **100K**
- Inserted in 4 chunks:
  - 20K
  - 30K
  - 30K
  - 20K

![IVFFLAT Incremental Results](./images/img5.png)

**Observation**

- At the third insertion chunk, **no reindexing was required**
- Indicates stable clustering quality under incremental growth

---

## Notes

- No LSH layer in this branch
- Suitable for studying clustering stability and insertion dynamics
- Acts as a baseline for comparison with the two-layer architecture
