# Architecture

DATAFORGE H1 V1 is a local-first, single-user application. A Python standard-library `ThreadingHTTPServer` listens on loopback and serves the static frontend plus JSON APIs. The frontend is plain HTML, CSS, and JavaScript; it uses `fetch()` for backend operations and has no build-time dependency or external CDN requirement.

## Boundaries

- `frontend/`: navigation and presentation; no dataset source-of-truth data is embedded in the dashboard.
- `backend/server.py`: request parsing, path allowlisting for static files, API routing, response serialization, and error mapping.
- `backend/storage.py`: manifest reconciliation, JSONL file IO, pagination, atomic writes, version backups, merge/split/delete, categories, and file-derived analytics.
- `backend/validator.py`: shared record validation used by import, generation, editing, and full-library checks.
- `backend/import_export.py`: JSONL/JSON/CSV parsing and export encoding; whole-library ZIPs are generated from active files.
- `backend/generator.py` + `backend/knowledge_bank.py`: bounded local generation and optional OpenAI-compatible generation.
- `backend/settings.py` / `backend/config.py`: persisted UI preferences and atomic configuration files. API keys remain in process environment variables.
- `backend/training.py`: training configuration, dependency/hardware probes, preflight, progress state, and a background worker.
- `training_engine.py`: preserved Transformers-based training implementation.

## Persistence rules

Dataset JSONL files are the primary record store. `datasets/manifest.json` is descriptive metadata. The backend re-counts active records from files when responding to analytics and dataset listings. File writes use temporary files in the same directory followed by an atomic replace. Edits and deletes copy the original file to `backups/` first.

The editor and Explorer load records in pages. Search scans JSONL records incrementally. The full-library duplicate pass retains canonical record fingerprints rather than all decoded record objects.

## Local security model

The default launcher accepts loopback bind addresses only. Static routes are resolved under `frontend/`; dataset file paths are validated against the approved `datasets/behavior/` and `datasets/knowledge/` parents. There is no remote multi-user access control because the application is intended for one local user. Do not expose the service on a LAN or public interface without adding authentication, CSRF protection, and an explicit deployment security model.
