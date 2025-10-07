# [Train] Training Llama 3.2 3B w/t Automation

## Summary
Training on Llama3.2 3B Instruct Model and Llama 3.2 3B (Base Model)

Both model training with:
* Training dataset: 800k Tokens ~ 59662 pairs of (instruction, input, output).
* Splitting: 80/10/10 - training/validate/testing. 
* Airflow will use the Runpod GraphQL API to manage pods. Dags will trigger to create->execute training->monitor->terminate GPU pod on Runpod. 
    * Attach with the created storage (which have training source code, dataset for training and venv there). 
* Logs metrics/parameter/artifacts with MLflow: URL expose via cloudflared: https://mlflow.checkitouts.site
    * For model logs/metrics/parameter store on local where the MLflow host.
    * For model artifacts/ checkpoints/output store on the volume storage of the Runpod.

**Needs Airflow and Mlflow enhancement: aim to run multiple dags script to trigger training at the same time and logs to MLflow tracking of multiple experiments in parallel.

## Summary Training Result on Mlflow:

### 1. Llama3.2 3B Instruct Model
![Llama3.2 3B Instruct Model - Run Details](image-1)
![Llama3.2 3B Instruct Model - Experiment Comparison](image-2)

### 2. Llama3.2 3B Based Model
![Llama3.2 3B Based Model - Run Details](image-3)
![Llama3.2 3B Based Model - Experiment Comparison](image-4)
