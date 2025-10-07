# [Inference] Configuration - Optimal Deployment Parameters

## Objective
Find the best optimal parameters to achieve higher throughput and goodput.

## Configuration Parameters

### RANDOM Dataset
- `INPUT_LEN=128` (based on llama3B tokenizer)
- `OUTPUT_LEN=384` (based on llama3B tokenizer)
- `MAX_MODEL_LEN=4096`

### Tuner Constraints
- `MIN_CACHE_HIT_PCT=30` - minimum prefix cache hit rate
- `MAX_LATENCY_ALLOWED_MS=13000` - 13s max latency

### Batching Search
- `NUM_SEQS_LIST="128 256 512"` - the list of candidate values for max number of concurrent sequences (i.e., how many open sequences/requests vLLM will try to batch together). Larger → more batching → higher throughput, but more memory use and higher per request latency.
- `NUM_BATCHED_TOKENS_LIST="4096 8192 16384"` - candidate values for max number of tokens batched per decoding iteration. Higher → more tokens grouped in one GPU pass → higher throughput but more memory and potentially higher latency.

## Issues Encountered

### Server Startup Timeout
The auto tune script waits 10 minutes for the server to start but after that:
- **ERROR**: Server did not start within 10 minutes. Check server log at `auto-benchmark/2025_09_02_19_41/vllm_log_gpu_memory_utilization_.93.log`

However, the vLLM log last 20 lines showed successful health checks:
```
(APIServer pid=1748) INFO:     127.0.0.1:60620 - "GET /health HTTP/1.1" 200 OK
(APIServer pid=1748) INFO:     127.0.0.1:54544 - "GET /health HTTP/1.1" 200 OK
(APIServer pid=1748) INFO:     127.0.0.1:54550 - "GET /health HTTP/1.1" 200 OK
... (multiple successful health checks)
```

### Possible Cause
Miscommunication between the script and the vLLM engine server → **Fixed**

## Auto-Tune Results

**Best Parameters Found:**
- `best_max_num_seqs: 512`
- `best_num_batched_tokens: 4096`
- `best_throughput: 19.63`
- `e2el: 3217.15 ms`

## Benchmark Results with Optimal Parameters

### Test Configuration
- `--request-rate 46` when benchmark
- Server ran with additional params: `--max-num-seqs 512 --max-num-batched-tokens 4096`

### Performance Comparison
*(Concurrency=100, Prompts=400)*

| Metric | Before | After | Improvement |
|--------|--------|-------|-------------|
| **Mean TTFT** | ~2.21 sec | ~0.85 sec | 61.5% faster |
| **P99 TTFT** | ~7.21 sec | ~2.25 sec | 68.8% faster |
| **Mean TPOT** | ~25.27 ms | ~13.67 ms | 45.9% faster |
| **Mean ITL** | ~25.20 ms | ~13.59 ms | 46.1% faster |
| **Mean E2EL** | ~13.27 sec | ~6.81 sec | 48.7% faster |
| **P99 E2EL** | ~20.00 sec | ~9.26 sec | 53.7% faster |
| **Request Throughput** | ~6.60 | ~12.47 | 88.9% increase |

### Key Insights

#### Time to First Token (TTFT)
- Before: ~2.21 sec, After: ~0.85 sec
- P99 TTFT: Before ~7.21 sec, After ~2.25 sec (only 1% of TTFT is longer than 2.25 sec)

#### Token Per Output Time (TPOT)
- Before: ~25.27 ms, After: ~13.67 ms
- On average it takes ~13.67 ms to generate one token

#### Inter-Token Latency (ITL)
- Before: ~25.20 ms, After: ~13.59 ms
- Indicates smoother token streaming

#### End-to-End Latency (E2EL)
- Mean: Before ~13.27 sec, After ~6.81 sec
- P99: Before ~20.00 sec, After ~9.26 sec (1% of requests take longer than 9.26 sec)

#### Request Throughput
- Before: ~6.60, After: ~12.47
- Shows improved request handling capacity

## Why These Parameters Reduced Latency

### `--max-num-seqs 512`
This increases the number of sequences (i.e., concurrent requests or prompts) that can be processed in a single forward pass.
- **Benefit**: Improves GPU utilization and reduces per-request overhead, especially under high concurrency.

### `--max-num-batched-tokens 4096`
This sets a higher cap on the total number of tokens processed in a batch.
- **Benefit**: Enables larger batches, which improves throughput and amortizes compute cost across more tokens.

### Better Batching Efficiency
These parameters allow the scheduler to pack more sequences and tokens into each batch.
- **Benefit**: Reduces idle time and improves memory bandwidth usage.

### Improved KV Cache Reuse
With more tokens and sequences per batch, the key-value cache is used more efficiently.
- **Benefit**: Speeds up decoding and reduces memory fragmentation.

## Important Notes

⚠️ **ABOVE MEASUREMENTS ARE FROM BASE MODEL AND DO NOT REFLECT THE ADAPTER FINE-TUNED MODEL.**

### Trade-offs
- **Increased VRAM Usage**: These parameters use more GPU VRAM.

### Conflict Identified
When tested in real-time scenario, 1 request takes 20-30 seconds. 

**Root Cause**: The auto-tune script did not tune with LoRA adapter in mind. When testing without LoRA adapter, 1 request falls under P99 end-to-end latency ~11 secs.

## Next Steps

1. Change the auto-tune script to fine-tune with **LoRA Adapter enabled**
2. Find out why LoRA adapter adds ~10s latency
3. **Target Date**: Next month 10-2025
