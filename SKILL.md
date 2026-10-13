---
name: fine-tune-llm
description: >-
  Use when guiding or tracking a small-LLM fine-tuning project (data rights,
  dataset, QLoRA/MLX training, eval, deploy) with a progress JSON file.
---
# fine-tune-llm

Guide and track end-to-end fine-tuning of a small model (about 0.5B–3B) on a narrow task. Prefer quality examples over model size.

## Source guide (keep linked)

Primary article to follow and cite when explaining steps:

- [How To Fine-Tune a Small LLM on Your Own Data (Full Guide)](https://x.com/sairahul1/status/2100882424343265527) — @sairahul1

Readable mirror if X is blocked: [Thread Navigator copy](https://threadnavigator.com/thread/2100882424343265527/)

Do not invent alternate steps that contradict that guide unless the user explicitly overrides them (e.g. chat-moderation labels instead of pitch-deck PDFs, MLX on Apple Silicon instead of bitsandbytes QLoRA).

## Progress tracking (required)

**One new project folder per fine-tune run.** Each new fine-tuning task (or isolated retrain) must start in a newly created directory. Do not reuse or mix artifacts with a previous run’s folder.

Suggested naming: `fine-tune-<short-task>-<YYYYMMDD>` (optionally under a parent such as `~/fine-tunes/…`). Example: `~/fine-tunes/fine-tune-chat-moderation-20261013`.

Resume an existing folder only when the user explicitly continues that run. A new task still gets a new sibling folder.

On every run of this skill:

1. Create a brand-new project root for this run (or resume an existing one only if the user explicitly continues that run). Set `project_root` in the progress JSON to that absolute path.
2. Keep all run artifacts inside that folder: `fine-tune-progress.json`, data, adapters, eval outputs, manifests, and related files.
3. Use a single progress file: `<project_root>/fine-tune-progress.json`.
4. If missing, create it from the template below (`status: not_started` for all steps).
5. Before starting work, read the JSON and show a short status summary (done / in_progress / blocked / skipped / not_started).
6. After finishing or blocking a step, update that step’s fields and write the file back immediately.
7. Never mark a step `done` without the listed evidence for that step.
8. Keep one active `in_progress` step at a time unless the user asks to parallelize.

### Progress JSON template

```json
{
  "schema_version": 1,
  "project_name": "",
  "task": "",
  "base_model": "Qwen/Qwen2.5-1.5B-Instruct",
  "training_stack": "qlora-cuda",
  "article_url": "https://x.com/sairahul1/status/2100882424343265527",
  "project_root": "",
  "progress_file": "fine-tune-progress.json",
  "updated_at": "",
  "current_step": 0,
  "notes": "",
  "steps": [
    {
      "id": 0,
      "key": "baselines",
      "title": "Build prompt-only and RAG baselines before training",
      "status": "not_started",
      "evidence": [],
      "blocker": null,
      "updated_at": null
    },
    {
      "id": 1,
      "key": "pick_model",
      "title": "Pick a small base model (0.5B–3B)",
      "status": "not_started",
      "evidence": [],
      "blocker": null,
      "updated_at": null
    },
    {
      "id": 2,
      "key": "data_rights",
      "title": "Record data rights before downloading",
      "status": "not_started",
      "evidence": [],
      "blocker": null,
      "updated_at": null
    },
    {
      "id": 3,
      "key": "download_data",
      "title": "Download source data reproducibly (allowlist + hashes)",
      "status": "not_started",
      "evidence": [],
      "blocker": null,
      "updated_at": null
    },
    {
      "id": 4,
      "key": "extract_text",
      "title": "Extract text (PDF/OCR or native chat export)",
      "status": "not_started",
      "evidence": [],
      "blocker": null,
      "updated_at": null
    },
    {
      "id": 5,
      "key": "clean_text",
      "title": "Clean text without corrupting facts/numbers",
      "status": "not_started",
      "evidence": [],
      "blocker": null,
      "updated_at": null
    },
    {
      "id": 6,
      "key": "build_dataset",
      "title": "Build human-reviewed training examples (JSONL)",
      "status": "not_started",
      "evidence": [],
      "blocker": null,
      "updated_at": null
    },
    {
      "id": 7,
      "key": "split_data",
      "title": "Split train/val/test by source entity (not by page/message leak)",
      "status": "not_started",
      "evidence": [],
      "blocker": null,
      "updated_at": null
    },
    {
      "id": 8,
      "key": "setup_env",
      "title": "Set up training environment on the right machine",
      "status": "not_started",
      "evidence": [],
      "blocker": null,
      "updated_at": null
    },
    {
      "id": 9,
      "key": "train",
      "title": "Train adapter (QLoRA on CUDA or LoRA/MLX on Apple Silicon)",
      "status": "not_started",
      "evidence": [],
      "blocker": null,
      "updated_at": null
    },
    {
      "id": 10,
      "key": "colab_or_gpu",
      "title": "Run first experiments on Colab / cheap GPU if needed",
      "status": "not_started",
      "evidence": [],
      "blocker": null,
      "updated_at": null
    },
    {
      "id": 11,
      "key": "gpu_strategy",
      "title": "Choose GPU provider strategy for longer runs",
      "status": "not_started",
      "evidence": [],
      "blocker": null,
      "updated_at": null
    },
    {
      "id": 12,
      "key": "evaluate",
      "title": "Evaluate against prompt-only baseline on held-out data",
      "status": "not_started",
      "evidence": [],
      "blocker": null,
      "updated_at": null
    },
    {
      "id": 13,
      "key": "serve",
      "title": "Serve the adapter behind a gateway",
      "status": "not_started",
      "evidence": [],
      "blocker": null,
      "updated_at": null
    },
    {
      "id": 14,
      "key": "monitor",
      "title": "Monitor quality and plan retrains",
      "status": "not_started",
      "evidence": [],
      "blocker": null,
      "updated_at": null
    }
  ]
}
```

Allowed `status` values: `not_started` | `in_progress` | `done` | `blocked` | `skipped`.

`training_stack` values: `qlora-cuda` | `mlx-apple` | `other`.

When updating a step:

- keep `project_root` set to this run’s absolute folder path
- set `updated_at` to ISO-8601 in the user’s timezone label if known
- set top-level `updated_at` and `current_step` to the active step id
- append concrete paths, command outputs, metric numbers, or file hashes to `evidence` (never secrets)
- if blocked, put a short reason in `blocker` and stop advancing until resolved or explicitly skipped

## Operating rules

- Start every new fine-tuning task or isolated retrain in a newly created project folder (e.g. `fine-tune-<short-task>-<YYYYMMDD>` or under `~/fine-tunes/…`). Do not reuse or mix with a previous run’s folder; resume a folder only when the user explicitly continues that run.
- Keep all run artifacts (`fine-tune-progress.json`, data, adapters, eval outputs) inside that folder and set `project_root` to its absolute path.
- Fine-tuning is not the first step. Complete baselines (step 0) or mark why they are skipped with user approval.
- Fine-tuning improves tone/structure/consistency for one narrow task; it does not replace retrieval for fresh facts.
- Prefer small models first; only scale up if evaluation fails.
- Raw source files stay private; commit processing code, schemas, hashes, and eval code when sharing.
- Training examples must be human-reviewed task pairs (input → desired output), not raw PDF pages or unlabeled dumps.
- Split by company/user/source so the same entity does not leak across train and val/test.
- Do not bump epochs just because training loss falls; watch validation and held-out metrics.
- Never deploy without a rollback path (previous adapter path recorded in evidence).
- Adapt the article’s pitch-deck example to the user’s real task (e.g. chat moderation: message(+context) → pass/fail + optional category/confidence).

## Step guidance (align with the article)

### 0 — Baselines
Build prompt-only, then prompt+RAG if facts are needed. Record failure modes that fine-tuning should fix.

Evidence examples: baseline prompt file, sample outputs, scores.

### 1 — Pick model
Default starting point from the article: `Qwen/Qwen2.5-1.5B-Instruct`. Alternatives in the same size band are fine.

Evidence: chosen model id + why.

### 2 — Data rights
For every source, record owner, rights status, commercial/redistribution scope, review date. Do not download until rights are clear.

Evidence: path to rights manifest (redact private emails).

### 3 — Download reproducibly
Allowlist URLs/files only. Store sha256 + timestamp in a download manifest. Skip duplicates by content hash.

Evidence: `download-manifest.json` path.

### 4 — Extract text
For PDFs: native extract → layout OCR → review. Do not silently drop empty OCR pages. For chat moderation, import labeled exports instead of OCR.

Evidence: extraction stats, sample page/message records.

### 5 — Clean text
Normalize whitespace/unicode only. Do not auto-“fix” numbers or labels with another LLM.

Evidence: cleaning script path + before/after sample count.

### 6 — Build dataset
Create reviewed JSONL examples matching the production I/O shape. Target often 200–500 strong examples to start.

Evidence: `train` candidate count, schema sample (no private content).

### 7 — Split
Train/val/test (e.g. 80/10/10) by entity. Freeze the test set after first peek.

Evidence: split manifest with entity ids only.

### 8 — Environment
Use a machine with enough memory/GPU (or Apple Silicon + MLX). A tiny VPS is for gateway/auth/queue/logs, not training or heavy inference.

Evidence: hardware note, package freeze file path.

### 9 — Train
CUDA path: QLoRA (4-bit NF4 + LoRA adapter) as in the article. Apple Silicon path: MLX LoRA/QLoRA-equivalent. Start with conservative epochs; checkpoint often.

Evidence: adapter path, hyperparams, train/val loss notes.

### 10–11 — Colab / GPU providers
Use Colab for first experiments; spot/dedicated GPUs for longer or production serving. Budget retries and repeated experiments.

Evidence: provider, GPU type, approximate GPU hours/cost.

### 12 — Evaluate
Compare to the prompt-only baseline on held-out data. Task-specific metrics (for moderation: precision/recall/F1, false positive rate at chosen threshold).

Evidence: scorecard comparing baseline vs fine-tuned.

### 13 — Serve
Serve adapter behind HTTPS auth / rate limit / queue. Do not expose raw model ports publicly.

Evidence: serve command or compose file path, healthcheck URL (no secrets).

### 14 — Monitor & retrain
Track latency, errors, user/report rates, model+adapter version. Retrain only from reviewed, permission-cleared, opt-in data with a rollback artifact.

For an isolated retrain (new training cycle treated as its own run), create a new sibling project folder—do not overwrite or mix into the previous run’s directory. Resume the existing folder only when the user explicitly continues that same run. Keep the prior adapter path as the rollback reference in evidence.

Evidence: monitoring checklist + version record; for a new retrain folder, `project_root` absolute path and link/note to the prior run’s adapter.

## Conversation flow when this skill runs

1. If this is a new fine-tuning task or isolated retrain: create a new project folder (e.g. `fine-tune-<short-task>-<YYYYMMDD>`, optionally under `~/fine-tunes/…`). Resume an existing folder only when the user explicitly continues that run.
2. Load or create `<project_root>/fine-tune-progress.json`, with `project_root` set to that folder’s absolute path. Keep all artifacts for this run inside the folder.
3. Summarize progress in plain language (one short paragraph + which step is next).
4. Do the next actionable step with the user (or mark blocked/skipped).
5. Write JSON after each change.
6. When all required steps are `done` or intentionally `skipped`, give a final handoff: artifact paths, metrics, rollback, and open risks.

## Out of scope

- Building a general chatbot or “making the model smarter” without a narrow task
- Scraping without rights review
- Claiming production readiness from training loss alone
