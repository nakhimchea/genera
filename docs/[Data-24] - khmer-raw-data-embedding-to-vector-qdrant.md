# [Data] Khmer Raw Data Embedding to Vector Qdrant

* Purpose: Implement a pipeline to embed raw Khmer textual data into dense vectors using E5-Large (1024-d) and store them in Qdrant for semantic search, deduplication, and downstream QA dataset preparation.
* Process Flow: 
    * Raw Data Ingestion
        * Text normalized (Unicode NFC, whitespace cleanup).
        * Hash generated for exact deduplication.
    * Embedding
        * Model: intfloat/multilingual-e5-large
        * Prefix: "passage: …" for semantic encoding.
        * Output: 1024-dimensional normalized vector.
    * Storage in Qdrant
        * Vector field: qa (cosine distance).
        * Payload fields:
            * content (raw Khmer text)
            * hash (sha256)
            * source (data origin)
            * len (character count)
* Example: {
*   "id": "216e5aa8-335f-4ef3-a9c3-2ba25119c1df",
*   "vector": {
*     "qa": [0.0123, -0.3456, 0.9812, ...]
*   },
*   "payload": {
*     "hash": "1d73e931fb66556bf2bb3f6a50df8d618087cc55c086a373bfb85d9aa1c1763",
*     "source": "ams",
*     "len": 2306,
*     "content": "ភ្នំពេញ៖ …",
*     "lang": "km",
*     "embed_model": "intfloat/multilingual-e5-large",
*     "embed_dim": 1024
*   }
* 
* }
* Outcome:  
