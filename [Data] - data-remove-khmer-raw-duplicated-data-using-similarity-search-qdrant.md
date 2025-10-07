# [Data] Remove Khmer Raw Duplicated Data using similarity search qdrant

* Purpose: Remove Duplication of Khmer Raw Data by using Qdrant to do vector similarity search,
* Deduplication & Processing Conditions: 
    * THRESH_COS = 0.97 → consider two texts near-duplicates if cosine similarity ≥ 0.97.
    * JACCARD_THRESH = 0.90 → require ≥ 90% word overlap for stricter duplicate filtering.
    * LEN_RATIO_MIN = 0.7 → only drop if lengths are reasonably close (≥ 70%).
    * DEDUP_LIMIT = 5 → check up to 5 nearest neighbors in Qdrant for duplication.
    * HNSW_EF = 96 → HNSW search accuracy parameter (higher = more accurate).
* Chunking & batching: 
    * MAX_CHARS = 512 → split long texts into chunks of 512 characters.
    * BATCH_SIZE = 8 → encode 8 chunks at a time for efficiency.
* Outcomes: It successfully deduplicated Khmer raw data. The code will be upload to Gitlab. 
