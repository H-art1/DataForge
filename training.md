# Training workflow

Training Studio separates data preparation from model execution. Select datasets, configure the model, method, split, and parameters, then run preflight. Preflight checks library membership, record totals, train/validation counts, hardware caps, and installed Python dependencies.

When the user starts a job, `backend/training.py` calls the preserved `training_engine.train()` function in a worker thread. The engine loads the tokenizer and causal language model through Transformers, tokenizes the selected data, holds out the configured validation subset, configures LoRA/QLoRA when selected, and runs `Trainer.train()`. For a hold-out dataset it explicitly calls `Trainer.evaluate()` at the end. Progress and metrics are passed to the Training Monitor.

Model/tokenizer compatibility is checked at load time rather than guessed from the model ID. A successful preflight means the configuration and dependencies passed local checks; it does not guarantee that a remote model repository is accessible or that a model fits into available memory. Failed model loads are displayed as training failures, not reported as successful runs.

Artifacts are saved under `training_runs/<run-name>/`, with final model and tokenizer under `final/` and run settings, selected files, hardware snapshot, and evaluation metrics in `training_config.json`. Existing non-empty run folders are not overwritten.

The core app needs no ML packages. LoRA/Full training needs an appropriate PyTorch build plus Transformers, Accelerate, Datasets, and Safetensors; LoRA adds PEFT. QLoRA additionally requires CUDA-capable PyTorch and bitsandbytes. Install a platform-appropriate PyTorch wheel before installing the optional requirements files.
