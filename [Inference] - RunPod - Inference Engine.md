# RunPod - Inference Engine

Set up vLLM engine RunPod
Set up with different fine-tuned models from huggingface.

## Setup Parameters Description

* `--host 0.0.0.0`: Listen on all network interfaces.
* `--port 8000`: Use port 8000 for the server.
* `--model Qwen/Qwen2.5-1.5B`: Load the specified model from Hugging Face.
* `--enable-lora`: Enable LoRA (Low-Rank Adaptation) support.
* `--lora-modules qwen-instruct=Vira21/Qwen2.5-1.5B-Instruct`: Apply LoRA weights from the given module to the base model.
* `--max-lora-rank 64`: Set the maximum rank for LoRA layers.
* `--api-key {api_key}`: Use API key for authentication.
* `--tensor-parallel-size 2`: Split model across 2 GPUs for parallel processing.
* `--gpu-memory-utilization 0.9`: Use up to 90% of GPU memory.
* `--dtype bfloat16`: Use bfloat16 precision for faster and memory-efficient computation.
* `--enforce-eager`: Force eager execution mode (no lazy evaluation).
* `--kv-cache-dtype auto`: Automatically choose the best data type for KV cache.
* `--block-size 16`: Set block size for token batching.
* `--max-num-seqs 512`: Allow up to 512 sequences in a batch.
* `--max-num-batched-tokens 16384`: Limit total tokens per batch to 16,384.
* `--distributed-executor-backend mp`: Use multiprocessing backend for distributed execution.

## Example Request to the Inference

```bash
curl -X POST "pod_url/v1/chat/completions" \
-H "Content-Type: application/json" \
-H "Authorization: Bearer {api_key}" \
-d '{
  "model": "qwen-instruct", //call the lora module
  "messages": [
    {
      "role": "system",
      "content": "អ្នកគឺជាជំនួយការវេជ្ចសាស្រ្តដែលមានចំណេះដឹង"
    },
    {
      "role": "user",
      "content": "តើការហាត់ប្រាណប៉ុន្មានដងក្នុងមួយសប្តាហ៍គឺល្អសម្រាប់សុខភាព?"
    }
  ],
  "max_tokens": 512,
  "top_p": 0.95,
  "temperature": 0.3,
  "stream": false
}'
```
