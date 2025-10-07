# [Train] DS-RunPod - Trigger Automations & Connections

## Workflow

Upload training dataset & code resource into Runpod volume → Airflow Dag trigger Runpod API to create/start/monitor pods → run task training scripts, logs artifacts on Runpod volume → MLflow task logs metrics/models/parameters → Training end (save model output & checkpoints) → terminate pods.

## Implementation Steps

### 1. Set up/Host Airflow and MLflow container on local machine
   - Airflow
   - MLflow
   - Docker compose

### 2. Expose port Airflow & MLflow via Cloudflare

### 3. Runpod Network Volume
Store training dataset, coding resource for training, model checkpoints/output

- **Create a persistent network volume on Runpod**
- **Upload all the training dataset and coding resource for training**
  - Using rclone to transfer the data/training source code from drive into RunPod volume
  - In volume directory, create Python venv to install all requirement packages and libraries

### 4. Airflow Dags tasks
Manage the pod through Runpod GraphQL API

- Trigger to create pod
- Monitoring pod
- MLflow log task
- Terminate pod

## Experiment Training Summary

### Dataset & Model
- **Training dataset**: 29,918 pairs of (instruction, input, output), 80/10/10 - training/validate/testing
- **Model training**: Llama-3.2-3B-Instruction
- **Tokenizer**: Merges PrahokBart tokens into Llama tokenizer
- **BLEU evaluation**

### Airflow Pod Configuration
- **GPU**: NVIDIA H100 80GB HBM3 (2 GPUs)
- **Image**: `runpod/pytorch:2.4.0-py3.11-cuda12.4.1-devel-ubuntu22.04`
- **Storage**: Attached with the created network volume

### Training Parameters
- **Epochs**: 1
- **Batch size**: 4
- **Learning rate**: 2e-05
- **Quantization**: 4-bit quantization
- **Logging**: MLflow

### Results

#### Training Duration
- **Training only**: ~2h30mins
- **Training + BLEU evaluations**: ~3h51mins

#### BLEU Scores
- **Validation BLEU (greedy)**: 10.75
- **Validation BLEU (beam)**: 9.17
- **Validation BLEU (contrastive)**: 9.37
- **Test BLEU (greedy)**: 11.02
- **Test BLEU (beam)**: 9.61
- **Test BLEU (contrastive)**: 9.80

#### MLflow Metrics
- **Epoch**: 1
- **Eval loss**: 8.93005943298339
- **Eval runtime**: 289.88s
- **Train runtime**: 8620.07s
- **Train samples per second**: 2.777
- **Final train loss**: 36.435128604666452
- **Final train perplexity**: 6.66155013021E+13

## MLflow Tracking

Training completed successfully! Results can be viewed at:
- **Run**: https://mlflow.checkitouts.site/#/experiments/2/runs/e38baa7548c9447f965a44d6f1a8771e
- **Experiment**: https://mlflow.checkitouts.site/#/experiments/2
