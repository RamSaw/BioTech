# Development Guidelines & Best Practices

## 1. General Python Coding Best Practices

- **No Conversational Meta-Commentary:** Do not include conversational meta-commentary between
  developers/AIs in the code. Keep those communications out of the code.
- **Follow Standard Style:** Adhere to PEP 8 guidelines. Choose a standard line length constraint
  (e.g., 80 or 100 characters) and use automated formatters and linters.
- **Type Annotations:** Extensively use type hints to make code self-documenting and compatible with
  static analysis. Prefer `collections.abc` types like `Mapping`, `Sequence`, `Iterable`, etc., in
  function arguments instead of concrete internal types like `dict` or `list` to make your functions
  more generic and robust. For local variable assignments involving complex types, and ALL global
  variables, declare explicit types.
- **Clear Naming Conventions:** Use `lower_snake_case` for variables, functions, and attributes. Use
  `UpperCamelCase` for classes and `UPPER_SNAKE_CASE` for constants. Pandas DataFrames must be
  exclusively suffixed with `_df` (e.g., `raw_data_df`).
- **Modularize Logic:** Avoid dumping all logic into a single monolithic script. Extract cohesive
  functionality into dedicated, testable Python modules.
- **Avoid Mutable Defaults:** Never use mutable objects (like lists or dictionaries) as default
  arguments in functions to prevent unintended state leakage.
- **Prefer Immutable Types:** You **must** utilize immutable data structures (e.g., `tuple`,
  `frozenset`, `MappingProxyType`, `NamedTuple`, or `dataclasses` with `frozen=True`) whenever
  possible to reduce unintended side effects and accidental state mutations. This includes
  explicitly protecting nested dictionaries or global mappings.
- **Exception Handling:** Catch specific exceptions instead of broad ones, and handle expected
  failure modes gracefully.
- **Docstrings:** Document the purpose, arguments, and return types of non-trivial functions and
  classes using a consistent docstring style.
- **Immutable Function Arguments:** Never modify input arguments (especially DataFrames) in place.
  Always create a copy (e.g., `df.copy()`) before performing transformations. Otherwise you will
  mutate the original object, which is a side effect.

## 2. Jupyter Notebook & Colab Best Practices

- **Notebook is a Story:** The notebook should tell a story of the research process. It should be
  logical, with clear steps, easy to follow and understand.
- **Do not over-validate:** If in previous cells some invariants/properties about the data are
  established, you do not need to re-validate them in further cells, just write code in assumption
  that they are true.
- **Concise Code:** Do not leave verbose comments in code if the code is self-explanatory or if
  clarity is requested. Focus on the logic.
- **Variables Scope:** Global variables are dangerous and have disadvantages. If you cannot avoid
  them, explicitly declare them wrapped in a class (e.g. `class Globals: data = None`). If not using
  a class structure, they **must** be UPPER-CASE, declared with an explicit type hint, and where
  possible, immutable (`MappingProxyType` or `frozenset`).
- **No Plain Code in Cells:** Avoid writing plain execution code directly in the cell. Extract logic
  into functions or methods and only call them sequentially in the cells.
- **Idempotent Cells:** Design cells to be idempotent. Executing the same cell multiple times should
  yield exactly the same result and not mutate shared state in unexpected ways.
- **Execution Order Discipline:** Always write notebooks with the assumption they will be executed
  linearly from top to bottom. Use "Restart and Run All" regularly to verify reproducibility.
- **Reproducible Environments:** Make notebooks fully reproducible. Pin notebook dependencies
  explicitly and strictly. Have a dedicated cell at the very top using the `%pip` magic command to
  install exactly the versions you used (e.g., `%pip install pandas==3.0.0 numpy==2.4.2`). This
  guarantees that anyone running your notebook elsewhere will use the exact same versions. Always
  fix seed in random operations whenever possible.
- **Notebook Structure:** Establish a clear Markdown hierarchy. Dedicate the very first cell to
  necessary setup, imports, and global constant initialization. Markdown cells are documentation for
  the *reader*; you must never include conversational meta-commentary between developers/AIs (e.g.,
  "As requested, here is the updated function..."). Keep those communications out of the notebook.
- **Data & Memory Management:** For biological research with large datasets, cleanly separate data
  extraction, transformation, and memory-heavy operations in distinct cells. Explicitly delete large
  redundant objects if facing memory limits.
- **Clear Outputs Before Committing:** Clear execution outputs before checking notebooks into
  version control to minimize noise and conflicts in git diffs.

## 3. Data Management & Version Control

- **Small Datasets (< 50MB):** Commit raw datasets (like `.csv` files) directly to Git. This
  guarantees absolute reproducibility without relying on external URLs that might break.
- **Large/Binary Datasets (> 50MB):** Do not commit large files or frequently changing data directly
  to Git to prevent repository bloat. Use Git LFS, DVC, cloud storage (AWS S3/GCS), or Hugging Face
  datasets. If hosted externally, the notebook's first cell should download the precise version of
  the dataset.
- **Data Directory Structure:** Always organize data carefully within the project repository.
  Segregate raw, untouched inputs (e.g., downloaded from IEDB) into a `data/raw/` directory. Store
  processed outputs generated by notebooks in a separate `data/processed/` directory.

## 4. Linting & Formatting

- **Mandatory Tools:** You **MUST** use `pyink` and `mdformat` for formatting and `pylint` for
  linting.
- **Workflow:** After every code modification (including notebooks), you **MUST** run:
  - `pyink .` (or `nbqa pyink .` for notebooks) to fix formatting.
  - `mdformat .` (or `nbqa mdformat . --nbqa-md` for notebooks) to fix formatting of markdown.
  - `pylint <file>` (or `nbqa pylint <file>`) to check for errors.
- **Strict Compliance:** In CI and final checks, use these flags to enforce zero tolerance:
  - **Formatting:** `pyink --check .`, `mdformat . --check`, `nbqa pyink --check .`, `nbqa mdformat . --nbqa-md --nbqa-diff` - fails if any file needs formatting.
  - **Linting:** `pylint <file> || pylint-exit -efail -wfail -cfail -rfail $?` (and similarly for
    `nbqa`). We enforce failure on Errors, Warnings, Conventions, and Refactors.
- **Fix Violations:** You must fix **ALL** linting errors and warnings. Zero tolerance policy: if
  `pylint` reports a warning, treat it as an error.
- **CI Checks:** Automated CI runs these strict commands. Ensure your code passes locally first.

## 5. Dependency Management

- **pyproject.toml:** Used primarily for **development dependencies** (linters, formatters, tools).
- **Runtime Dependencies:** Managed directly in notebooks using `%pip install ...` to ensure
  self-contained reproducibility. Do NOT list them in `pyproject.toml` unless they are needed for
  shared library code.
- **Version Constraints:** When adding dependencies to `pyproject.toml`, you **MUST** restrict them
  to the current major version to prevent breaking changes (e.g., `pylint>=4.2.0,<5.0.0`). Allowing
  the next major version is forbidden.
- **Lock File:** `requirements-dev.lock` captures the exact versions of development dependencies.
- **Updating Dependencies:**
  - After modifying `pyproject.toml`, you **MUST** update the lock file immediately.
  - Command: `pip-compile pyproject.toml --extra dev --output-file requirements-dev.lock` (requires
    `pip-tools`).
- **Installing Dependencies:** Use `pip install -r requirements-dev.lock` to set up the dev
  environment.

## 6. Tips

- **Restart Frequently:** When debugging complex environment state, restart the notebook kernel
  instead of continually overwriting global variables.
- **Limit Cell Complexity:** Keep cells short and focused on one logical step. If a cell requires
  excessive scrolling, its logic should likely be refactored into an external file.
- **Hide Auxiliary Code:** For presentation or demonstration notebooks, consider hiding complex
  utility functions to keep attention focused on research results.
- **Mind Magics Usage:** Shell commands (`!`) and magic commands (`%%time`) are very useful but
  tightly couple the notebook to the underlying environment. Use them judiciously.
- **Track Progress for Long Jobs:** For long-running biological data processing loops, rely on
  progress bar utilities (like `tqdm`) rather than printing thousands of raw log lines.

## 7. Agent Instructions (Meta-Rules)

- **Context Awareness:** At the start of every task, you **MUST** reread all currently open/pending
  files and any files you plan to edit. Do not rely on cached memory of file states.
- **Respect User Changes:** Do **NOT** revert or overwrite user changes unless strictly necessary
  for the current objective. If you must overwrite a user modification, you likely misunderstood the
  intent—ask for clarification first.

## 8. Protocols & Workflows

### The `[begin: branch_name]` Command

When the user types `[begin: branch_name]`, you **MUST** create a new branch from `main` with the
specified `branch_name`.

### The `[finish]` Command

When the user types `[finish]` (or similar), you **MUST** perform the following pre-push checklist:

1. **Regenerate Lock File:** Always regenerate `requirements-dev.lock` to ensure it is fresh. If
   there are no changes, it updates nothing.
1. **Documentation Sync:**
   - Update all `README.md` files if content is outdated.
   - Update all `spec.md` files to match the current implementation.
   - **Reflect Feedback:** Review conversation history for any unaddressed feedback or general
     instructions and update `development_guide.md` accordingly.
1. **Config Check:** Verify `pyproject.toml` and other config files are clean and correct.
1. **Format:** Run `pyink .`, `nbqa pyink .`, `mdformat .` and `nbqa mdformat . --nbqa-md` (allow
   them to fix files).
1. **Run Tests:** Execute all CI (read `ci.yml` file) checks locally (e.g., `pylint`, `nbqa`, etc.)
   and ensure they pass.
1. **Create PR:** Create a Pull Request into `main` with a title and description written by you
   based on the pending changes.
