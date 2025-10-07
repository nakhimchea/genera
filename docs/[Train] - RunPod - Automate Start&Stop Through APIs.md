# [Train] RunPod - Automate Start&Stop Through APIs

## Overview

Airflow will use the RunPod GraphQL API to create/start/monitor/terminate pods automatically for training workflows.

## Architecture

* **Host Airflow and MLflow container on local machine**
* **Expose both services port via Cloudflared tunnel:**
    * https://mlflow.checkitouts.site
    * https://airflow.checkitouts.site

## Workflow

DAGs call RunPod API to trigger the following sequence:
1. **Create** - Initialize a new RunPod instance
2. **Start** - Start the pod with specified configuration
3. **Monitor** - Track pod status and training progress
4. **Log to MLflow** - Record training metrics and parameters
5. **Terminate** - Clean up and stop the pod after completion

## Screenshots

### RunPod Console
Shows the active training pod with GPU configuration (RTX A4000 x1) and cost information ($0.25/hr).

### Airflow DAG Logs
Displays the execution logs for the RunPod GPU training task, including:
- GPU configuration attempts (NVIDIA RTX A4000, SECURE CloudType, Bid-0.25)
- Pod creation and triggering process
- Task execution status and downstream task scheduling

### Airflow DAG Graph View
Visualizes the workflow pipeline:
- `trigger_runpod` → `monitor_runpod` → `mlflow_log` → `terminate_runpod`
- All tasks shown with success status (PythonOperator)

### MLflow Experiment Overview
Displays training run parameters and results:
- **Pod Configuration:**
  - pod_id: `7ids0trn0nk2kl`
  - template_id: `runpod-torch-v280`
  - final_status: `EXITED`
  - pod_type: `interruptable`
  - cost_per_hr: `0.25`
  - machine_id: `9xtvto8qobv8`
  - cloud_type: `SECURE`
  - bid_per_gpu: `0.25`
  - gpu_count: `1`
- **Training Parameters:**
  - learning_rate: `5e-05`
  - batch_size: `16`
- **Metrics:**
  - training_accuracy: `0.85`
  - validation_loss: `0.32`

### MLflow Model Metrics
Charts showing:
- Training accuracy progression (0.85 final value)
- Validation loss over time (0.32 final value)

## Benefits

- **Cost Optimization**: Automatic pod termination prevents unnecessary charges
- **Experiment Tracking**: MLflow integration captures all training metrics and parameters
- **Reproducibility**: Complete workflow automation ensures consistent training runs
- **Monitoring**: Real-time tracking of pod status and training progress
- **Scalability**: Easy to trigger multiple training runs with different configurations
