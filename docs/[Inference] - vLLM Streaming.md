# [Inference] vLLM Streaming - Research

Research and benchmark vLLM's streaming inference for chat applications (OpenAI-compatible /v1/chat/completions) using AsyncLLMEngine. Measured streaming-specific metrics (Time To First Token — TTFT, Time Per Output Token — TPOT, Inter-Token Latency — ITL) across multiple concurrency configurations and payload sizes.

## Test environment
* Model tested: Llama-3.2-3B-Khmer-QA (LoRA adapter experiments noted)
* Endpoint: OpenAI-compatible v1/chat/completions (vLLM server)
* A100 GPU 2x on RunPod.

## Benchmark Results

| max_conc | tokens/sec (output_throughput) | mean TTFT (ms) | median TTFT (ms) | p99 TTFT (ms) | mean TPOT (ms) | p99 TPOT (ms) | mean ITL (ms) | p99 ITL (ms) | mean E2E (ms) |
|----------|-------------------------------|----------------|------------------|---------------|----------------|---------------|---------------|--------------|---------------|
| 1        | 83.02                         | 1,954 ms       | 1,727 ms         | 3,152 ms      | 8.24 ms        | 8.60 ms       | 8.22 ms       | 51.29 ms     | 6,164 ms      |
| 10       | 340.05                        | 9,971 ms       | 10,142 ms        | 11,089 ms     | 9.16 ms        | 13.79 ms      | 9.82 ms       | 49.47 ms     | 14,652 ms     |
| 20       | 473.95                        | 15,070 ms      | 14,199 ms        | 30,444 ms     | 10.34 ms       | 18.43 ms      | 43.38 ms      | 1,026 ms     | 20,353 ms     |
| 40       | 495.94                        | 34,950 ms      | 31,009 ms        | 65,818 ms     | 6.96 ms        | 11.82 ms      | 47.32 ms      | 1,943 ms     | 38,505 ms     |
| 70       | 444.12                        | 52,970 ms      | 54,686 ms        | 104,368 ms    | 7.16 ms        | 17.55 ms      | 107.75 ms     | 2,749 ms     | 56,628 ms     |
| 100      | 205.78                        | 38,344 ms      | 30,413 ms        | 109,834 ms    | 6.80 ms        | 24.36 ms      | 1,514.92 ms   | 8,237 ms     | 41,821 ms     |

## Notable observations

* **cuDAGraphs effect**: enabling cuDAGraphs reduced E2E latency substantially in our experiments — we measured ~8 s E2E with the LoRA adapter and ~5 s with the base model when cuDAGraphs were enabled.

* **--no-enforce-eager tradeoff**: adding `--no-enforce-eager` allows cuDAGraphs to be used, which improved runtime latency but increases VRAM usage and increases server init time. The higher VRAM and longer cold-start time were acceptable trade-offs for the observed inference latency improvements.

* **Single-request streaming is smooth**: single concurrency runs show low TPOT (~8–10 ms) and low ITL, which produces a fluent token-by-token stream once generation starts — TTFT (~2s) is the main contributor to user-perceived delay in single-user cases.
![Image 1 - Single Request Streaming](image1.png)
* **High concurrency increases queuing and p99 TTFT**: multi-user scenarios show larger TTFT and E2E (often tens of seconds), even when token throughput is high. That means high aggregate tokens/sec does not directly translate to a smooth per-user streaming experience. Below is demo of during 20 concurrent request 60 input prompts.
![Image 2 - 20 Concurrent Request Streaming](image2.png)
* **Stop markers / include_stop_str_in_output**: when `include_stop_str_in_output=True` the stop marker will be emitted at the streamed chunks `|<im_end>|` — ensure client post-processing strips/handles it if not desired.
