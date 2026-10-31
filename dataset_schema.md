# Dataset schema and storage

Each active dataset is a UTF-8 JSONL file under `datasets/behavior/` or `datasets/knowledge/`. Each nonblank line is one object with a `messages` list. The object may contain extra top-level fields, but the baseline training contract requires a non-empty user message and assistant message. The runtime validator also checks role names, empty values, short assistant responses, and exact duplicates within a dataset.

The JSON Schema reference is `schemas/chat_messages.schema.json`. Metadata entries are defined by `schemas/dataset_metadata.schema.json` and stored in `datasets/manifest.json`.

## Example record

```json
{"messages":[{"role":"user","content":"What does a database index do?"},{"role":"assistant","content":"A database index is an auxiliary structure that helps the database locate rows without scanning every row. Its design trades extra storage and write work for faster reads on selected query patterns."}]}
```

Use JSONL as the canonical format. CSV import maps prompt/response-like columns into `messages`; JSON import accepts arrays or an object with a `records` array. Export can emit JSONL, JSON with metadata, CSV, or a ZIP of the entire active library.

Do not treat the metadata manifest as a record source. If a JSONL file has changed outside DATAFORGE, its live record count is reconciled by the storage layer; descriptions, tags, category, and version remain metadata until updated in the application.
