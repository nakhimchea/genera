# Performance Benchmark

## Overview

Raw performance of the model via "vllm bench latency"

Measure the latency of processing a single batch of requests to understand the baseline performance of a model in isolation, raw processing speed of the model on specific hardware setup under controlled conditions without external variables introduced like internet speed, connections, etc.

## Hardware Specifications

- **RAM:** 24GB
- **CPU:** M4: 14 Cores

## Test 1

### Parameters

- `--num-iters-warmup 1`
- `--batch-size 5`
- `--input-len 32`
- `--output-len 128`
- `--max_num_batched_tokens 32768`
  - (Required because: Value error, max_num_batched_tokens (4096) is smaller than max_model_len (32768))

### Results

| Metric | Latency (seconds) |
|--------|------------------|
| Avg latency | 14.367676368166455 |
| 10% percentile | 13.886254237500543 |
| 25% percentile | 14.040829802498592 |
| 50% percentile | 14.299527604000104 |
| 75% percentile | 14.484979447748628 |
| 90% percentile | 14.696015079199787 |
| 99% percentile | 16.14409600793064 |

### Summary

Benchmark test was run on local hardware using small input 32 tokens and output 128 tokens lengths with a batch size of 5 resulting in an average latency of ~14.37 seconds across 30 iterations.
