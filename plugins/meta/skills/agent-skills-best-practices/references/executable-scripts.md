# Executable Scripts

Default the deterministic work to scripts. Delegate parsing, validation, normalization, selection, transformation, file generation, and repeatable checks whenever exact inputs and outputs can be specified. Use model reasoning only when the work requires judgment that cannot be encoded as a deterministic procedure.

Before writing a script, inspect the target repository and runtime for existing helpers and built-in standard-library modules or functions. Prefer those primitives over third-party dependencies and manual reimplementations.

Default the standalone skill scripts to Python because its standard library covers most scripting needs without an installation step. When the script genuinely needs a third-party dependency, declare it with [PEP 723](https://peps.python.org/pep-0723/) inline metadata and lock it while authoring the script. For example, use `pydantic` when decoded JSON enters Python as `Any` and must be parsed into a trusted contract:

<authoring_example>

```bash
uv add --script script.py pydantic   # writes the PEP 723 header
uv lock --script script.py           # writes script.py.lock
```

</authoring_example>

Commit the adjacent `script.py.lock` with the script. At runtime, `SKILL.md` shows only `uv run --locked script.py`. That command already installs the locked dependencies before running the script, which obsoletes dedicated `uv add` or `uv sync` setup steps that only introduce instruction noise and bloat prompts.

Follow the [`uv` script guide](https://docs.astral.sh/uv/guides/scripts/) for the workflow and [`uv` storage documentation](https://docs.astral.sh/uv/reference/storage/) for its writable cache. `uv` creates the dedicated script environment in that cache rather than beside the skill, so the packaged script and lockfile can work even in a read-only mount (i.e., avoiding/sidestepping the need for a mutable `.venv` in the skill distribution).

State in `SKILL.md` that script paths are relative to the directory that contains `SKILL.md` for the sake of example. The actual script location should be introspected from the runtime environment and ultimately adapted to the resolved skill directory.

Show each script's command, arguments, and output path in `SKILL.md`. Do not describe how the script implementation details work. The agent should treat the script as a black box, reading only the exit status and output contract.
