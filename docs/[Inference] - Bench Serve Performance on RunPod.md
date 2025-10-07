# Bench Serve Performance on RunPod

## Specs
This memory can store 1,310,368 tokens for KV cache in total.

## Params
```bash
--host 0.0.0.0 
--port 8000 
--model meta-llama/Llama-3.2-3B-Instruct 
--enable-lora 
--lora-modules Khmer-QA=Vira21/Llama-3.2-3B-Khmer-QA 
--served-model-name "Khmer-LLM" 
--max-lora-rank 64 
--api-key {{API_KEY}} 
--tensor-parallel-size 2 
--gpu-memory-utilization 0.9 
--dtype bfloat16 
--enforce-eager 
--kv-cache-dtype auto 
--block-size 16 
--max-num-seqs 512 
--distributed-executor-backend mp
```

## Issues on 27-08-2025

### Problems
* vllm bench serve gave unspecified error "400 bad request" when benchmarking v1/chat/completions endpoint
* official documents doesn't provide enough info on how to use custom dataset and benchmark the v1/chat/completions endpoint

### Solution
* using custom data (1% portion of the 500k khmer qa) required jsonl format: `{"prompt": "ដថាសកថ"}`
* throw `--endpoint-type openai-chat` when using chat completions endpoint
* `--custom-output-len 512`

## GPU Usages

### 1. Max concurrent 1, input prompt 10, output token length 512
This seem slower than larger input prompt since it can be considered as a warm up for the engine

### 2. Max concurrent 10, input prompt 50, output token length 512

### 3. Max concurrent 70, input prompt 200, output token length 512

### 4. Max concurrent 100, input prompt 400, output token length 512

## Summary

* **Low Concurrency (10 concurrency, 50 prompts):**
    * Time to first token: ~2 seconds, quick first token.
    * End2End Latency: ~12.9 seconds, short wait for full response.

* **Medium Concurrency (40–70 concurrency, 100–200 prompts):**
    * Time to first token: ~2.9–3.2 seconds, noticeable but acceptable delay.
    * End2End Latency: ~13.6–14 seconds, longer wait for full response.

* **High Concurrency (100 concurrency, 400 prompts):**
    * Time to first token: ~2.2 seconds, improved due to batching.
    * End2End Latency: ~13.3 seconds, consistent with medium load. ~6.6 requests completed per second.
