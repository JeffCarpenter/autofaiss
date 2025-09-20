# Repository Guidelines

## Project Structure & Module Organization
The `autofaiss/` package hosts production code grouped into `indices/`, `metrics/`, `utils/`, and `external/`. Keep index-building logic under `indices/` (for example, extend `indices/index_factory.py` when wiring new index types), evaluation helpers in `metrics/`, and shared numpy/faiss utilities in `utils/`. Third-party touchpoints (PySpark, S3, CLI entrypoints) belong in `external/`; isolate vendor-specific imports there. Tests live in `tests/unit/` and mirror the package tree (`tests/unit/test_optimize.py` exercises `external/optimize.py`). Usage samples and notebooks stay in `examples/` or `docs/`.

## Build, Test, and Development Commands
- `make install` creates an editable environment with runtime dependencies.
- `make install-dev` pulls extras from `requirements-test.txt` before running checks.
- `make lint` runs `mypy`, `pylint`, and `black --check -l 120 autofaiss`; fix formatting with `python -m black -l 120 autofaiss` and address type/style feedback locally.
- `make test` executes `pytest -x -v --cov=autofaiss --cov-report term-missing tests/unit` for fast coverage-aware feedback.
- Release artefacts can be produced with `make build-dist` (sdist) or `make build-pex` (standalone PEX binary).

## Coding Style & Naming Conventions
Black (120-character width) governs formatting; never hand-wrap lines unless readability demands it. Use 4-space indentation, snake_case for modules, functions, and variables, and PascalCase for classes. Type annotate public APIs—`mypy.ini` targets Python 3.10 with `ignore_missing_imports = True`, so guard optional imports under `typing.TYPE_CHECKING` when necessary. Prefer `logging.getLogger(__name__)` over `print`, matching recent refactors in `indices/distributed.py`.

## Testing Guidelines
Name tests `test_<behavior>` and colocate them with the code they validate (e.g., `tests/unit/test_build.py`). Keep fixtures lightweight; rely on synthetic NumPy arrays instead of large binaries. Run `make test` before committing and review the uncovered-line report produced by `--cov-report term-missing`. For distributed or external service flows, mock clients rather than hitting live clusters.

## Commit & Pull Request Guidelines
Write imperative, sentence-case commit subjects under ~72 characters (e.g., `Fix lint issues in distributed.py logging`). Group related edits and squash trivial fix-ups before pushing. Pull requests should explain motivation, list validation commands (`make lint`, `make test`), link issues when applicable, and attach logs or screenshots for visible changes. Tag reviewers who own the touched subsystem and note follow-up work explicitly.

## Security & Configuration Tips
Do not commit generated FAISS indexes or large datasets; reference external storage paths instead. Keep credentials, Spark configs, and `.env` content out of version control (see `.gitignore`). After dependency bumps, run `python check_version.py` to refresh published compatibility metadata. Use environment variables for cloud endpoints and rotate keys outside the repository.
