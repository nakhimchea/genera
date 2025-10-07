# [Train] Training Qwen 3B Automation

## Summary Training on Qwen 3B

* **Training dataset**: 800k Tokens ~ 59662 pairs of (instruction, input, output).
* **Splitting**: 80/10/10 - training/validate/testing.
* **Airflow** will use the Runpod GraphQL API to manage pods. Dags will trigger to create->execute training->monitor->terminate GPU pod on Runpod.
    * Attach with the created storage (which have training source code, dataset for training and venv there).
* **Logs metrics/parameter/artifacts with MLflow**: URL expose via cloudflared: https://mlflow.checkitouts.site 
    * For model logs/metrics/parameter store on local where the MLflow host.
    * For model artifacts/ checkpoints/output store on the volume storage of the Runpod. 

## Summary Training Results on MLflow

### Training Parameters

| Parameter | Value |
|-----------|-------|
| dataset_id | /workspace/llm-storage/data/training_qa_500k.jsonl |
| output_dir | /workspace/llm-storage/output/qwen-3B |
| model_name | Qwen/Qwen2.5-3B |
| max_length | 512 |
| batch_size | 2 |
| eval_batch_size | 1 |
| epochs | 5 |
| learning_rate | 5e-05 |
| gradient_checkpointing | False |
| lora_r | 32 |
| lora_alpha | 64 |
| lora_dropout | 0.1 |
| lora_target_modules | ['q_proj', 'k_proj', 'v_proj', 'o_proj', 'gate_proj', 'up_proj', 'down_proj'] |
| use_4bit | False |

### Training Metrics

| Metric | Value |
|--------|-------|
| bleu_greedy | 4.50886935435673 |
| epoch | 4.99995809763252 |
| grad_norm | 6.32726526260376 |
| learning_rate | 5.58742456976831e-8 |
| loss | 3.0856 |
| eval_bleu_greedy | 4.50886935435673 |
| eval_loss | 0.649633049964948 |
| eval_runtime | 272.6213 |
| eval_samples_per_second | 0.367 |
| eval_steps_per_second | 0.367 |
| total_flos | 2.077837312439353e+17 |
| train_loss | 0.820218518545194 |
| train_runtime | 7863.5113 |
| train_samples_per_second | 30.348 |
| train_steps_per_second | 1.897 |

### Training Charts

The MLflow experiment tracking shows:
- **Epoch progression**: Linear increase from 0 to 5 epochs
- **Gradient norm**: Fluctuating between 4-12 throughout training
- **Loss**: Decreasing from ~10 to ~3 over the training period
- **Evaluation loss**: Decreasing from ~0.7 to ~0.45
- **BLEU greedy score**: Stable around 2.5-4.5
- **Learning rate**: Linear decay from 50μ to near 0

### Experiment Details

- **Experiment**: qwen-khmer-qa
- **Run ID**: run-20250918-024324
- **MLflow URL**: https://mlflow.checkitouts.site/#/experiments/5/runs/c65fa78f132e448d89ff4781a8b79679
