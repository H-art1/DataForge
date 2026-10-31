# DATAFORGE H1 V1

**A local-first AI dataset workspace for building, inspecting, validating, and preparing instruction-tuning data.**

DATAFORGE H1 V1 replaces the V13.3 terminal-first workflow with a desktop-style application that runs in the browser while its Python service and dataset files remain on the local machine. The runtime application uses Python's standard library; model-training packages are optional and are checked before training can begin.

## Product highlights

- Premium dark desktop-style interface, with an optional saved light theme.
- Dashboard, searchable Dataset Library, Dataset Explorer, Behaviour and Knowledge collections, Generator, paginated Dataset Editor, Validator, Training Studio, Export and Import Centers, Analytics, Settings, and About.
- **1,300 active datasets**: 600 behaviour datasets and 700 knowledge datasets.
- **6,500 validated JSONL records**: 3,000 behaviour and 3,500 knowledge examples. The full library scan in this release found zero schema errors, zero validation warnings, zero repeated user prompts, and zero exact duplicate records across files.
- Dataset counts, record counts, category distribution, storage use, and recent file changes are calculated from the active on-disk library.
- JSONL as the primary format; JSON and CSV import/export, plus whole-library ZIP export.
- Record-level editing with 50-record pages, versioned saves, recoverable backups, merge, split, delete, and validation.
- Local curated dataset generation, or optional OpenAI-compatible provider integration using an environment variable for the API key.
- Training configuration and hardware/dependency preflight, real Transformers/PEFT model training when the compatible stack is installed, hold-out validation during training, progress, logs, and saved metrics.
- The original V13.3 terminal application and original dataset library are retained for migration and reference.

> **Important distinction:** changing the configured AI display name changes DATAFORGE's UI identity only. It does not modify model weights or change the underlying model. Preparing or exporting a dataset, or saving a training configuration, is not model training. Training only occurs after the Training Studio starts the Transformers training process successfully.

## Requirements

### Core application

- Python 3.10 or later.
- Windows, macOS, or Linux.
- A modern browser.
- No third-party Python packages are required to run the dataset management application.

### Optional model training

- A compatible Python environment and model/tokenizer accessible from the machine.
- PyTorch, Transformers, PEFT, Accelerate, Datasets, and Safetensors for LoRA/QLoRA workflows.
- QLoRA additionally requires a compatible CUDA-enabled PyTorch build and `bitsandbytes`.
- Full fine-tuning uses the Transformers training stack without PEFT adapters.
- Hardware limits are probed locally and configuration values above the detected caps are rejected. A model may still require more memory than the generic hardware cap predicts.

No model weights or provider API keys are bundled.

## Installation

### Windows

1. Extract `DATAFORGE_H1_V1.zip` into a folder where you want the application to live.
2. Install Python 3.10+ and enable the Python PATH option in the installer.
3. Double-click `Install_DataForge.bat` to verify Python, compile the application, and optionally create a Desktop shortcut.
4. Start with `run.bat` or the Desktop shortcut.

You can also start directly from Command Prompt:

```bat
cd path\to\DATAFORGE_H1_V1
py -3 datasetmaker.py
```

### macOS / Linux

```bash
cd /path/to/DATAFORGE_H1_V1
./install.sh
./run.sh
```

Or run the Python entry point directly:

```bash
python3 datasetmaker.py
```

The server binds to `127.0.0.1` and opens the browser at `http://127.0.0.1:8765/` (or the next available local port). Keep the terminal window open while using DATAFORGE. Press `Ctrl+C` in that terminal to stop the server. For a non-browser launch:

```bash
python datasetmaker.py --no-browser
```

The launcher intentionally supports loopback addresses only. It is not designed to be exposed to a public network.

## Quick start

1. Launch DATAFORGE.
2. Open **Dataset Library** to search the 1,300 active datasets or choose Behaviour / Knowledge in the sidebar.
3. Open a dataset in **Dataset Explorer** to inspect its metadata and paginated records.
4. Use **Dataset Editor** for paginated record edits. Save a page to create a new dataset version and a full-file backup.
5. Use **Dataset Generator** to preview locally generated examples or provider-generated data before saving.
6. Use **Data Validator** to validate one dataset or the full active library.
7. Export JSONL, JSON, CSV, or a ZIP from **Export Center**.
8. Configure a model and selected datasets in **Training Studio**. Run preflight before starting actual training.

## Project architecture

```text
DATAFORGE_H1_V1/
├── backend/                 Python application services and domain logic
│   ├── config.py            Paths, defaults, atomic configuration persistence
│   ├── generator.py         Curated local and optional provider-backed generation
│   ├── import_export.py     JSONL, JSON, CSV and ZIP transformations
│   ├── knowledge_bank.py    Reviewed local educational topic seeds
│   ├── server.py            Local HTTP server and JSON API routes
│   ├── settings.py          AI identity, theme and provider settings
│   ├── storage.py           Manifest-backed dataset storage and CRUD operations
│   ├── training.py          Training configuration, preflight and job status
│   └── validator.py         Shared chat-record schema and quality checks
├── frontend/                Single-page browser application
│   ├── index.html           Application shell and navigation
│   ├── styles.css           Dark/light responsive design system
│   └── app.js               Navigation, views, interactions and API client
├── datasets/
│   ├── behavior/            Active behaviour JSONL datasets
│   ├── knowledge/           Active knowledge JSONL datasets
│   ├── manifest.json        Per-dataset metadata
│   └── index.json           Library index and summary counts
├── schemas/                 JSON Schema definitions for records and metadata
├── config/                  Runtime settings and example configuration files
├── backups/                 Automatic version and deleted-dataset backups
├── training_runs/           Actual training outputs and model artifacts
├── training_engine.py       Preserved Transformers/PEFT training implementation
├── legacy/                  Retained V13.3 CLI and original documentation
├── templates/               Original dataset templates used by the legacy CLI
├── dataset_library.py       Library-selection helper used by the legacy CLI
├── archive/                 Archived V13.3 dataset library and templates
├── scripts/                 Dataset migration and repair script
├── tests/                   Automated integrity, API and workflow tests
├── datasetmaker.py          Main application entry point
├── run.bat / run.sh         Platform launchers
└── README.md
```

The browser UI calls the Python API on the same loopback server. Dataset content remains in JSONL files; `datasets/manifest.json` stores metadata and the API reconciles counts against the actual files. The UI does not use hardcoded dashboard totals.

More detail: [`architecture.md`](architecture.md), [`dataset_schema.md`](dataset_schema.md), and [`training.md`](training.md).

## Dataset library and structure

### Included collections

| Collection | Datasets | Records |
|---|---:|---:|
| Behaviour | 600 | 3,000 |
| Knowledge | 700 | 3,500 |
| **Total** | **1,300** | **6,500** |

The behaviour library includes instruction following, context handling, reasoning, communication, planning, clarification, self-correction, uncertainty, safety, error recovery, teaching, summarization, dialogue, writing, creative work, and professional response behaviour. The knowledge library covers mathematics, science, computer science, AI/ML, programming, engineering, cybersecurity, databases, cloud and DevOps, business, economics, finance fundamentals, humanities, geography, history, and specialist technical topics.

The 500 topic-seed knowledge datasets were rewritten with topic-specific definitions, examples, comparisons, and limitations. All 3,000 behaviour prompts and 3,500 knowledge prompts are unique across the active library. The 600 behaviour datasets have variant-specific names, descriptions, and prompts rather than reusing the same five generic questions. The other 200 pre-existing topic-specific knowledge files were retained. The original V13.3 datasets, `base_dataset.jsonl`, and templates are also available inside `archive/v13_3_dataset_library.zip`.

### Primary JSONL record schema

Each non-empty line in an active dataset is one JSON object:

```json
{"messages":[
  {"role":"user","content":"Explain the purpose of a validation split."},
  {"role":"assistant","content":"A validation split holds out examples that are not used for gradient updates..."}
]}
```

Every record must contain a `messages` array, at least one non-empty `user` message, and at least one non-empty `assistant` message. Supported roles are `system`, `developer`, `user`, `assistant`, and `tool`. The full JSON Schema is in `schemas/chat_messages.schema.json`.

Each dataset metadata entry includes:

- `dataset_id`, `name`, `category`, `subcategory`, and `description`
- `number_of_records`, `format`, `creation_date`, `version`, and `tags`
- `quality_status`, `file`, and internal collection `kind` (`behavior` or `knowledge`)

Metadata schema: `schemas/dataset_metadata.schema.json`. The manifest is an index, not the source of truth for record counts: the storage layer reads current JSONL files when it calculates statistics.

## AI identity and provider settings

Open **Settings → AI Identity** to edit:

- AI display name (default: **My AI**)
- Description
- Personality preset
- Communication style
- Response tone
- Default behaviour profile
- Selected dataset IDs

The chosen name appears in the application identity areas and persists in `config/app_settings.json`. The identity profile is application configuration; it does not alter model weights, fine-tune a model, or change a provider's actual model identity.

### Optional AI provider

DATAFORGE supports an OpenAI-compatible chat-completions endpoint for dataset generation.

1. Open **Settings → AI provider integration**.
2. Set the provider base URL and model name.
3. Set an environment variable name, for example `OPENAI_API_KEY`.
4. In the operating system environment, set that variable to the key.
5. Enable the provider and save settings.

Example for a current terminal session:

```bash
export OPENAI_API_KEY="your-key"
```

PowerShell:

```powershell
$env:OPENAI_API_KEY = "your-key"
```

The key itself is never written to DATAFORGE settings or returned by the settings API. The key is read from the configured environment variable only when a generation request is made. Do not commit `.env` files or shell history containing credentials.

## Dataset generation

Open **Dataset Generator**, choose Behaviour or Knowledge, enter the topic, category, record count, difficulty, writing style, tone, and optional source notes, then choose **Generate preview**. Inspect the examples and validation result before saving.

- Without a provider, the local curated generator creates up to five structured records per request from the bundled reviewed topic seeds and behaviour patterns. Unknown knowledge topics require source notes; the local generator will not invent a seed definition for an unknown topic.
- With a provider configured, DATAFORGE can request up to 500 records from the selected compatible model. Returned data is parsed, schema-validated, checked for exact duplicates, and rejected when the provider does not return the requested unique record count.
- Existing records can also be created manually in Dataset Editor or imported from JSONL, JSON, or CSV.

Local generation is intentionally bounded rather than filling large requested counts with repeated filler examples.

## Dataset management

- **Search/filter:** search dataset names, descriptions, categories, tags, and IDs; filter by collection, category, and quality.
- **Explorer:** browse records and search within a dataset using bounded pages.
- **Editor:** edit 50 records at a time. Saving replaces only that page, preserves unseen records, increments the version, and creates a full-file backup first.
- **Merge:** select multiple datasets from the same collection; exact duplicate records are removed.
- **Split:** create train and validation partitions from an existing dataset.
- **Validation:** check message schema, required roles, empty content, short responses, local duplicates, and cross-library exact duplicates.
- **Delete:** deletion creates a backup under `backups/deleted/` before removing the active file.
- **Version history:** prior files from saves are stored under `backups/versions/`.
- **Import/export:** import validates before writing a new library file; exports are generated from the current data on disk.

Use the Export Center's ZIP option or copy the project folder to create a portable backup. Do not edit the manifest by hand while DATAFORGE is running.

## Import and export formats

- **JSONL:** one chat record per line; recommended for training.
- **JSON:** a JSON array of records, a single record object, or an export object containing a `records` array.
- **CSV:** requires prompt/response, user/assistant, instruction/output, or input/completion columns. It is converted into the standard chat message structure.
- **ZIP:** whole active library export with a manifest and the dataset JSONL files.

The import API has a 40 MB content limit. Invalid records or malformed JSON are rejected before any new dataset is created.

## Training Studio

Training Studio distinguishes four separate tasks:

1. **Dataset preparation:** choose validated JSONL files, inspect their records, and export them. This does not train a model.
2. **Training configuration:** select a model ID or local checkpoint, method (LoRA, QLoRA, or Full), dataset IDs, epochs, steps, batch, gradient accumulation, sequence length, rank, learning rate, seed, and validation split. Configurations can be saved or exported as JSON.
3. **Actual model training:** **Start actual training** launches the preserved Transformers training engine in a background worker. The engine loads the tokenizer and model, checks common LoRA target modules where relevant, applies the chosen method, trains, and saves model/tokenizer artifacts to `training_runs/`.
4. **Model evaluation:** when a validation split is configured, examples are held out from gradient updates and evaluated using the Trainer's evaluation workflow. Evaluation metrics are logged and saved in the run's `training_config.json`. This is validation during the training run; it is not a general-purpose benchmark suite.

Before training, run **Preflight checks**. DATAFORGE checks selected files, record counts, split sizes, method, installed dependencies, and hardware-derived parameter caps. QLoRA is blocked unless CUDA-enabled PyTorch is detected. Model/tokenizer compatibility is verified when Transformers attempts to load the selected model at start; a valid preflight cannot guarantee a remote model is reachable or that it fits in memory.

Install the optional stack only in a suitable Python environment:

```bash
# Install a PyTorch build appropriate for your OS and CPU/GPU first.
python -m pip install -r requirements-training.txt
```

For QLoRA, after CUDA PyTorch and the base stack are configured:

```bash
python -m pip install -r requirements-qlora.txt
```

PyTorch installation commands vary by platform and CUDA version; use the official PyTorch selector for the correct build. No training dependencies are needed for dataset editing, generation in local mode, validation, or export.

## Analytics

The Dashboard and Analytics pages calculate totals from active JSONL files and the manifest. They report dataset and record counts by collection, category distribution, storage bytes, quality statuses, file formats, and recent dataset modifications. The V1 inventory currently contains 1,300 active dataset files with 6,500 records.

## Tests and verification

From the project root:

```bash
python -m unittest discover -s tests -v
python -m py_compile datasetmaker.py backend/*.py training_engine.py
node --check frontend/app.js
```

The automated suite covers the full library inventory and global uniqueness, schema integrity, paged editing, CRUD/version backups, merge/split/delete, JSONL/JSON/CSV import-export, identity and theme persistence, local generator constraints, training configuration, and HTTP health/static/search/error routes.

A real model-training test requires optional model packages, a compatible model/tokenizer, and appropriate hardware; it is not simulated by the automated suite. The app reports missing dependencies and refuses to claim a training run has started when training cannot actually start.

## Configuration and storage locations

| Path | Purpose |
|---|---|
| `config/app_settings.json` | AI identity, theme, provider endpoint metadata, and preferences; created on first launch |
| `config/training_config.json` | Saved Training Studio configuration |
| `datasets/manifest.json` | Dataset metadata index |
| `datasets/behavior/` | Active behaviour JSONL files |
| `datasets/knowledge/` | Active knowledge JSONL files |
| `backups/versions/` | Prior dataset files before versioned edits |
| `backups/deleted/` | Files backed up before deletion |
| `training_runs/` | Actual training run artifacts and evaluation metrics |

Runtime settings and future dataset edits are local to the extracted project folder. Keep a separate copy of the folder or use ZIP export for backup.

## Troubleshooting

**Python is not found.** Install Python 3.10+, enable PATH integration, then open a new terminal. Windows supports `py -3 datasetmaker.py`; macOS/Linux supports `python3 datasetmaker.py`.

**The browser cannot connect.** Keep the launcher terminal open. Check whether a local security tool is blocking the chosen loopback port. DATAFORGE tries the requested port and the following 24 ports before reporting a bind error.

**Port 8765 is already in use.** DATAFORGE automatically selects the next available local port and prints the actual URL in the terminal.

**Provider generation reports a missing API key.** Confirm the environment variable name in Settings and that the variable is defined in the same environment that launched DATAFORGE. Restart DATAFORGE after changing persistent environment variables.

**Local generation requests more than five records.** The built-in generator is deliberately capped at five curated examples. Enable a provider for larger runs, or manually add/import reviewed records.

**Unknown knowledge topic is rejected.** Add reviewed source notes in the generator. Unknown local topics without notes are rejected to avoid fabricated subject matter.

**Training preflight reports missing dependencies.** Install the optional packages in the same Python environment used to launch DATAFORGE. For QLoRA, confirm `torch.cuda.is_available()` and install compatible `bitsandbytes`. CPU-only environments will not pass QLoRA preflight.

**Model loading fails after preflight.** Check the Hugging Face model ID or local checkpoint path, tokenizer availability, network access for gated/private model repositories, Transformers compatibility, model architecture, and available RAM/VRAM. See the full error in the Training Monitor.

**A dataset file is invalid.** Run Data Validator and inspect the dataset. Imports are rejected before saving when invalid; manual file changes may require a restore from `backups/` or rebuilding metadata.

**Recover a previous edit.** A copy is written under `backups/versions/<dataset-id>/` before every editor save. Deleted datasets are backed up under `backups/deleted/<dataset-id>/`.

## Release history

See [`CHANGELOG.md`](CHANGELOG.md) for the V13.3 → V1 transition.
