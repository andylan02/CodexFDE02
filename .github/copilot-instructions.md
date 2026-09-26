# Copilot instructions for CodexFDE02

This repository is the personal R&D workbench for the Codex AI Engineering Delivery camp, not a general-purpose app. The core product flow is: build the workbench and course harness in this repo, then use it to manage and validate delivery against an external FlowERP repo. Keep that boundary explicit.

## Environment and setup

Use the repo-local virtual environment and Windows-safe Python invocation:

```powershell
py -3.11 -m venv .venv
.\.venv\Scripts\python.exe -m pip install -e .
```

Or on macOS/Linux:

```bash
python3.11 -m venv .venv
source .venv/bin/activate
python -m pip install -e .
```

Common startup commands:

```powershell
python main.py
python -X utf8 -m workbench.cli serve-workbench
python -X utf8 -m workbench.cli serve
python -X utf8 -m workbench.cli environment-check
```

Project rules that matter here:

- Use `.venv` and the project interpreter; do not default to system Python.
- Keep `.runtime` and the FlowERP runtime separate; do not mix workbench DBs with customer DBs.
- The workbench UI is the normal entrypoint at `http://127.0.0.1:8001/`; FlowERP is separate at `http://127.0.0.1:8000/`.
- Keep this repo focused on orchestration, evidence, and course workflow; do not import ERP business logic into the repo.

## Build, test, and validation commands

There is no dedicated lint target in `pyproject.toml`. The quality signal is the Python unit tests plus the blocking Eval harness.

Run the full test suite:

```bash
python -X utf8 -m unittest discover -s tests -v
```

Run a single test file or single case:

```bash
python -X utf8 -m unittest tests.test_workbench -v
python -X utf8 -m unittest tests.test_workbench.TestWorkbenchCase.test_something -v
```

Run browser-state tests (`*.test.cjs`):

```bash
node --test tests/*.test.cjs
```

Run the blocking Eval gate used by CI:

```bash
python -X utf8 -m eval.harness --suite blocking
```

Run the full Eval suite (blocking + observing checks):

```bash
python -X utf8 -m eval.harness --suite all
```

Other repo-native checks used throughout the project:

```bash
python -X utf8 -m workbench.cli environment-check
python -X utf8 -m workbench.cli demo
python -X utf8 -m agent.loop --max-rounds 3
python -X utf8 -m agent.graph --max-rounds 3
python -X utf8 -m workbench.feedback summary
python -X utf8 -m workbench.cli course-status
```

Single-lesson checks:

```bash
python -X utf8 -m workbench.cli course-contract --lesson 4
python -X utf8 -m workbench.cli course-eval --lesson 4
```

## High-level architecture

This repo is layered around workbench-driven delivery rather than a conventional app structure.

- `workbench/` handles CLI commands, project registration, specs, execution, evidence, and runtime configuration.
- `eval/` is the repo’s quality gate and the authority for blocking checks; it writes reports under `.runtime/reports/` and is used by CI.
- `agent/` contains bounded loop/graph logic for multi-step task recovery and course progression.
- `workbench_web/` is the minimal dashboard entrypoint for the workbench; `harness_web/` is optional and not the course pass criterion.
- `main.py` bootstraps the local workbench and may also launch the external FlowERP service using saved runtime paths.

The important boundary is that FlowERP is intentionally kept outside this repository. This repo orchestrates the external project, records decisions and evidence, and runs validation in the correct project context without importing ERP business code into this tree.

## Key conventions

- Treat this as a delivery platform, not a loose collection of scripts: requirements, Spec, execution, review, and evidence must stay connected.
- Keep workbench state and ERP product state separate. Do not mix `.runtime` databases or runtime folders across repos.
- Prefer the smallest validation command that matches the change. If a fix is user-facing or delivery-critical, use the repo’s Eval harness rather than ad hoc checks.
- Preserve failure evidence and avoid rewriting failed runs into success narratives.
- When working with an external project, use the registered project root or `FLOWERP_PROJECT_ROOT` instead of hardcoding paths.
- Keep write scopes narrow and prefer isolated candidate patches / worktrees when task execution requires it.
- Course governance and lesson contracts in `AGENTS.md` and `README.md` are part of the operational contract; changes that affect course flow or delivery boundaries should respect them.

## Files worth checking before making a change

- `README.md` for repo goals and project boundaries
- `AGENTS.md` for course-governance and operational constraints
- `pyproject.toml` for install and test entry points
- `workbench/cli.py` for the command surface
- `eval/harness.py` for blocking eval semantics
- `workbench/project_registration.py` for external repo registration rules

## Recommended default approach

1. Confirm whether the change belongs to the workbench, the external FlowERP project, or the optional harness UI.
2. Prefer the local project environment and the smallest relevant validation command.
3. Keep the evidence trail intact when task state, review decisions, or patch selection changes.
4. Do not treat a green workbench check as proof that the external ERP project is valid unless the correct project-specific validation was run.
