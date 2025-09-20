# Repository Guidelines

## Project Structure & Module Organization
- `autofaiss/` is the core package; `indices/`, `metrics/`, and `utils/` host reusable primitives for building FAISS indexes, evaluation, and helpers. Keep shared logic here.
- `autofaiss/external/` maintains integrations (e.g., PySpark, S3, NumPy 2.x helpers). Touch these only when updating third-party boundaries.
- `tests/unit/` mirrors the package layout with `test_<topic>.py` files; add new coverage alongside the feature you touch.
- `examples/` and `docs/` provide runnable notebooks and narrative guides; use them for usage samples, not production code or fixtures.

## Build, Test, and Development Commands
- `make install` upgrades packaging tools, installs core requirements, and wires the repo in editable mode for local hacking.
- `make install-dev` installs test-only dependencies from `requirements-test.txt`.
- `make lint` runs `mypy`, `pylint`, and `black --check -l 120` over `autofaiss/` to catch typing, style, and formatting issues.
- `make test` executes `pytest -x -v --cov=autofaiss --cov-report term-missing tests/unit`, failing fast while reporting uncovered lines.
- `make black` formats the tree with Black (120 char width); run this before committing formatting-heavy updates.

## Coding Style & Naming Conventions
- Target Python 3.8 semantics; prefer type hints on public functions and keep imports optional-safe (`mypy` ignores missing stubs).
- Use 4-space indentation, snake_case module/function names, and PascalCase for classes. Align new modules with existing `indices_x.py` naming.
- Favor `logging` (see recent commits replacing `print`) and keep log levels consistent with surrounding code.
- Let Black control formatting; avoid manual line wraps under 120 characters unless readability drops.

## Testing Guidelines
- Add unit tests in `tests/unit/test_<feature>.py`; structure assertions as `test_<behavior>_*` for clarity.
- Run `make test` locally; the coverage floor is effectively "non-zero," so missing imports will be called out in the term report.
- Pytest runs with `log_cli_level=DEBUG`; leverage this for diagnosing distributed runs but mute noisy logs inside tests.
- When adding complex data flows, provide lightweight fixtures in the test file instead of committing binary assets.

## Commit & Pull Request Guidelines
- Follow the existing history: imperative, sentence-case subject lines under ~72 characters (e.g., "Add detailed debugging to PySpark merge operations").
- Squash trivial fix-ups before pushing; keep each commit logically scoped (feature, fix, or refactor).
- PRs should include: a concise summary, validation notes (`make lint`, `make test`), linked issues when relevant, and screenshots/log excerpts if behavior changes.
- Request review from maintainers familiar with the touched module and note any follow-up tasks to avoid surprises.

## Security & Configuration Tips
- Avoid committing generated FAISS indexes or large datasets; store them externally and reference paths in docs.
- Keep cloud credentials and Spark configs out of version control; rely on environment variables or `.env` files listed in `.gitignore`.
- When updating dependencies, re-run `check_version.py` to refresh compatibility metadata before publishing.
