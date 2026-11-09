# Executable Scripts

Delegate parsing, validation, normalization, selection, transformation, file generation, and repeatable checks to scripts whenever exact inputs and outputs can be specified.

Before writing a script, inspect the target repository and runtime for existing helpers and built-in standard-library modules or functions. Prefer those primitives over third-party dependencies and manual reimplementations.

Default the standalone skill scripts to Python. Its standard library covers most scripting needs without an installation step. When the script genuinely needs a third-party dependency, declare it with [PEP 723](https://peps.python.org/pep-0723/) inline metadata and lock it while authoring the script. For example, use `pydantic` when decoded JSON enters Python as `Any` and must be parsed into a trusted contract:

<authoring_example>

```bash
uv add --script script.py pydantic   # writes the PEP 723 header
uv lock --script script.py           # writes script.py.lock
```

</authoring_example>

Commit the adjacent `script.py.lock` with the script. At runtime, `SKILL.md` shows only `uv run --locked script.py`. That command already installs the locked dependencies before running the script. It makes dedicated `uv add` or `uv sync` setup steps obsolete, along with the instruction noise and prompt bloat they introduce.

Follow the [`uv` script guide](https://docs.astral.sh/uv/guides/scripts/) for the workflow and [`uv` storage documentation](https://docs.astral.sh/uv/reference/storage/) for its writable cache. `uv` creates the dedicated script environment in that cache rather than beside the skill, which lets the packaged script and lockfile work from a read-only mount without a mutable `.venv` in the skill distribution.

State in `SKILL.md` that its example script paths are relative to the directory that contains `SKILL.md`. The agent then resolves each path against the installed skill directory at runtime.

Show each script's command, arguments, and output path in `SKILL.md`. The exit status and output are the agent's whole contract with the script.
