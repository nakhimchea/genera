# [Train] DigitalOcean - Training

Fine-tuned Qwen2.5-1.5B-Instruct using public medical datasets https://huggingface.co/datasets/khopilot/khmer-medical-qa

## Training hyperparameters

The following hyperparameters were used during training:
* learning_rate: 2e-05
* train_batch_size: 4
* eval_batch_size: 8
* seed: 42
* gradient_accumulation_steps: 4
* total_train_batch_size: 16
* optimizer: Use adamw_torch with betas=(0.9,0.999) and epsilon=1e-08 and optimizer_args=No additional optimizer arguments
* lr_scheduler_type: linear
* lr_scheduler_warmup_ratio: 0.1
* num_epochs: 3

## Training results

| Training Loss | Epoch  | Step | Validation Loss |
|---------------|--------|------|-----------------|
| 3.1408        | 0.4353 | 500  | 0.7851          |
| 2.7979        | 0.8705 | 1000 | 0.7096          |
| 2.6743        | 1.3055 | 1500 | 0.6792          |
| 2.6213        | 1.7417 | 2000 | 0.6641          |
| 2.5961        | 2.1776 | 2500 | 0.6537          |
| 2.5341        | 2.6128 | 3000 | 0.6467          |

## Framework versions

* PEFT 0.17.0
* Transformers 4.47.0
* Pytorch 2.5.1+cu121
* Datasets 2.19.1
* Tokenizers 0.21.4

## Training Script

```python
#!/usr/bin/env python3
"""
Fine-tune Qwen 2.5 1.5B on Khmer Medical QA (LoRA + optional 4-bit)
- Loads dataset from Hugging Face: khopilot/khmer-medical-qa
- Auto-creates a validation split if missing
- TensorBoard logging (auto-start on :6006)
"""

import os
import argparse
import subprocess
from datetime import datetime

import torch
from datasets import load_dataset

from transformers import (
    AutoModelForCausalLM,
    AutoTokenizer,
    TrainingArguments,
    Trainer,
    DataCollatorForLanguageModeling,
    BitsAndBytesConfig,
    set_seed,
)

from peft import LoraConfig, get_peft_model, prepare_model_for_kbit_training


# --------------------------
# CLI
# --------------------------
def parse_args():
    p = argparse.ArgumentParser(description="Fine-tune Qwen 2.5 on Khmer Medical QA")
    p.add_argument("--model_id", default="Qwen/Qwen2.5-1.5B", help="Base model ID")
    p.add_argument("--dataset_id", default="khopilot/khmer-medical-qa", help="HF dataset repo")
    p.add_argument("--output_dir", default="models/khmer-medical-qwen", help="Output directory")
    p.add_argument("--num_epochs", type=int, default=3, help="Training epochs")
    p.add_argument("--batch_size", type=int, default=4, help="Per-device batch size")
    p.add_argument("--learning_rate", type=float, default=2e-5, help="Learning rate")
    p.add_argument("--gradient_checkpointing", action="store_true", help="Enable gradient checkpointing")
    p.add_argument("--use_4bit", action="store_true", help="Use 4-bit quantization (bnb)")
    p.add_argument("--lora_r", type=int, default=32, help="LoRA rank")
    p.add_argument("--val_size", type=float, default=0.02, help="Validation fraction if split missing")
    p.add_argument("--seed", type=int, default=42, help="Random seed")
    p.add_argument("--push_to_hub", action="store_true", help="Push final model to HF Hub")
    p.add_argument("--hub_model_id", default="Vira21/Qwen2.5-1.5B-Instruct", help="Hub model repo (user/repo or repo)")
    p.add_argument("--start_tensorboard", action="store_true", help="Auto-start TensorBoard on :6006")
    return p.parse_args()


# --------------------------
# Model & Tokenizer
# --------------------------
def load_tokenizer(model_id: str):
    print(f"🔄 Loading tokenizer: {model_id}")
    tok = AutoTokenizer.from_pretrained(model_id, trust_remote_code=True)
    if tok.pad_token is None:
        tok.pad_token = tok.eos_token
    return tok


def setup_model(args):
    print(f"🔄 Loading model: {args.model_id}")

    bnb_config = None
    if args.use_4bit:
        bnb_config = BitsAndBytesConfig(
            load_in_4bit=True,
            bnb_4bit_quant_type="nf4",
            bnb_4bit_compute_dtype=torch.bfloat16,
            bnb_4bit_use_double_quant=True,
        )
        print("✅ 4-bit quantization enabled")

    model = AutoModelForCausalLM.from_pretrained(
        args.model_id,
        quantization_config=bnb_config,
        torch_dtype=torch.bfloat16 if not args.use_4bit else None,
        device_map="auto",
        trust_remote_code=True,
    )

    if args.gradient_checkpointing:
        model.gradient_checkpointing_enable()
        print("✅ Gradient checkpointing enabled")

    if args.use_4bit:
        model = prepare_model_for_kbit_training(model)

    lora_config = LoraConfig(
        r=args.lora_r,
        lora_alpha=args.lora_r * 2,
        target_modules=["q_proj", "k_proj", "v_proj", "o_proj", "gate_proj", "up_proj", "down_proj"],
        lora_dropout=0.1,
        bias="none",
        task_type="CAUSAL_LM",
    )
    model = get_peft_model(model, lora_config)
    model.print_trainable_parameters()
    return model


# --------------------------
# Dataset
# --------------------------
def format_to_qwen_chat(example):
    """
    Build a single string with Qwen chat tags.
    Prefer Khmer; fallback to English when empty.
    """
    q = (example.get("question_km") or "").strip()
    a = (example.get("response_km") or "").strip()
    if not q:
        q = (example.get("question_en") or "").strip()
    if not a:
        a = (example.get("response_en") or "").strip()

    text = (
        "<|im_start|>system\n"
        "អ្នកគឺជាជំនួយការវេជ្ជសាស្ត្រដែលមានចំណេះដឹង។ សូមឆ្លើយសំណួរវេជ្ជសាស្ត្រដោយផ្តល់ព័ត៌មានត្រឹមត្រូវ និងមានប្រយោជន៍។<|im_end|>\n"
        "<|im_start|>user\n"
        f"{q}<|im_end|>\n"
        "<|im_start|>assistant\n"
        f"{a}<|im_end|>"
    )
    return {"text": text}


def load_and_prepare_dataset(args, tokenizer):
    print(f"📚 Loading dataset: {args.dataset_id}")
    ds = load_dataset(args.dataset_id)

    # If no validation split, create one from train
    if "validation" not in ds:
        print(f"ℹ️ No validation split found; creating {int(args.val_size*100)}% split from train...")
        ds_split = ds["train"].train_test_split(test_size=args.val_size, seed=args.seed, shuffle=True)
        ds = {"train": ds_split["train"], "validation": ds_split["test"]}
        # Convert to DatasetDict-like access
        from datasets import DatasetDict
        ds = DatasetDict(ds)

    # Map into chat text
    ds = ds.map(format_to_qwen_chat, remove_columns=[c for c in ds["train"].column_names if c != "text"])

    # Tokenize
    def tokenize_fn(batch):
        return tokenizer(
            batch["text"],
            truncation=True,
            padding="max_length",
            max_length=2048,
        )

    tokenized = ds.map(tokenize_fn, batched=True, remove_columns=["text"])
    print(f"✅ Prepared: train={len(tokenized['train'])}, val={len(tokenized['validation'])}")
    return tokenized


# --------------------------
# TensorBoard
# --------------------------
def maybe_start_tensorboard(output_dir: str, start: bool):
    if not start:
        return
    tb_log_dir = os.path.join(output_dir, "runs")
    os.makedirs(tb_log_dir, exist_ok=True)
    try:
        subprocess.Popen(
            ["tensorboard", f"--logdir={tb_log_dir}", "--host=0.0.0.0", "--port=6006"],
            stdout=subprocess.DEVNULL,
            stderr=subprocess.DEVNULL,
        )
        print(f"🧪 TensorBoard started → http://162.243.64.74:6006")
    except FileNotFoundError:
        print("⚠️ 'tensorboard' not found. Install with: pip install tensorboard")


# --------------------------
# Training Args & Main
# --------------------------
def build_training_args(args, tokenizer):
    run_name = f"qwen-khmer-medical-{datetime.now().strftime('%Y%m%d-%H%M%S')}"
    return TrainingArguments(
        output_dir=args.output_dir,
        overwrite_output_dir=True,
        num_train_epochs=args.num_epochs,
        per_device_train_batch_size=args.batch_size,
        per_device_eval_batch_size=args.batch_size,
        gradient_accumulation_steps=8 if args.batch_size < 4 else 4,
        gradient_checkpointing=args.gradient_checkpointing,
        warmup_ratio=0.1,
        learning_rate=args.learning_rate,
        logging_dir=os.path.join(args.output_dir, "runs"),
        logging_steps=20,
        save_steps=500,
        eval_steps=500,
        evaluation_strategy="steps",
        save_strategy="steps",
        save_total_limit=3,
        load_best_model_at_end=True,
        metric_for_best_model="eval_loss",
        greater_is_better=False,
        bf16=True,   # H200/A100 etc. prefer bf16
        tf32=True,
        dataloader_num_workers=4,
        remove_unused_columns=False,
        push_to_hub=args.push_to_hub,
        hub_model_id=args.hub_model_id,
        report_to=["tensorboard"],  # TensorBoard only
        run_name=run_name,
    )


def main():
    args = parse_args()
    set_seed(args.seed)

    tokenizer = load_tokenizer(args.model_id)
    model = setup_model(args)
    dataset = load_and_prepare_dataset(args, tokenizer)
    training_args = build_training_args(args, tokenizer)

    data_collator = DataCollatorForLanguageModeling(
        tokenizer=tokenizer,
        mlm=False,
        pad_to_multiple_of=8,
    )

    maybe_start_tensorboard(args.output_dir, args.start_tensorboard)

    trainer = Trainer(
        model=model,
        args=training_args,
        train_dataset=dataset["train"],
        eval_dataset=dataset["validation"],
        tokenizer=tokenizer,
        data_collator=data_collator,
    )

    print("🚀 Starting training...")
    trainer.train()

    print(f"💾 Saving to {args.output_dir}")
    trainer.save_model()
    tokenizer.save_pretrained(args.output_dir)

    if args.push_to_hub:
        print(f"📤 Pushing to Hugging Face Hub: {args.hub_model_id}")
        trainer.push_to_hub()

    print("✅ Done!")


if __name__ == "__main__":
    main()
```
