# Split agent workspace from CLI

## Outcome

Keep three independent Git repositories. No PyPI publishing.

```text
fio-sdk  <- Git dependency of fio-cli
fio-cli  <- Git dependency of fio-ai
docs 1, docs 2 <- pinned submodules of fio-ai
```

- `fio-sdk`: generated REST client only.
- `fio-cli`: Python CLI only. It exposes the existing `fio` console command.
- `fio-ai`: agent instructions, game reference material, and reproducible runtime workspace. It invokes `uv run fio`; it never imports `fio_cli`.

Use immutable release tags (for example `v0.1.0`) in direct Git references. Commit each repository's `uv.lock`; upgrading is an intentional tag and lockfile update.

## 1. Make CLI configuration workspace-relative

The installed CLI must load configuration from the workspace that invokes it, not from its installed package directory.

1. Replace `.env` loading with one CLI-internal TOML configuration loader.
2. Check for `config.toml` in the current project directory first.
3. Only when no project file exists, check `~/.config/fio/config.toml`.
4. If neither file exists, retain the current clear configuration error.
5. Retain `config.toml.example`; keep project-local `config.toml` ignored.

Configuration precedence:

1. `./config.toml` in the current project directory.
2. `~/.config/fio/config.toml`.
3. No file: configuration error.

The file format and keys are:

```toml
cx_code="REPLACE_ME"
user_name="REPLACE_ME"
company_code="REPLACE_ME"
company_name="REPLACE_ME"
fio_rest_key="REPLACE_ME"
```

This means `cd fio-cli && uv run fio ...` uses `fio-cli/config.toml` when present; otherwise it uses the shared user configuration. `fio-ai` behaves the same way.

Verification:

- Add a small subprocess test proving project `config.toml` is chosen over the user file.
- Add a companion check proving the user file is used when the project file is absent.
- Run the existing CLI test suite.

## 2. Version the SDK and CLI as Git packages

1. Tag the current working SDK commit with a semantic version.
2. Change the CLI's `fio-sdk` direct reference from its moving default branch to that tag.
3. Run `uv lock` and commit `uv.lock`.
4. Tag the tested CLI commit with a semantic version.

The CLI's existing `[project.scripts]` entry remains the executable contract:

```toml
[project.scripts]
fio = "fio_cli.main:main"
```

Do not publish either package to PyPI. Git plus tags and lockfiles supplies installation and reproducibility with no registry release process.

## 3. Create `fio-ai`

Create a minimal non-library uv project containing:

- `pyproject.toml`, `uv.lock`, `config.toml.example`, and `.gitignore`.
- `AGENTS.md` plus the analysis instructions moved from this repository.
- `GAME.md` and any agent-owned reference notes currently required by those instructions.
- the two external documentation repositories as pinned Git submodules under `vendor/`.
- a small `bootstrap` script that runs:

```sh
git submodule update --init --recursive
uv sync
```

Declare the CLI as a tagged direct Git dependency:

```toml
dependencies = [
	"fio-cli @ git+https://github.com/lumivient/fio-cli.git@v0.1.0",
]
```

Agents run the installed entry point from this workspace:

```sh
uv run fio context
```

Verification in a clean `fio-ai` checkout:

```sh
./bootstrap
uv run fio --help
uv run fio context
```

The final command must report the resolved `fio-ai/config.toml` context, or the shared user context when no project file exists, without printing the REST key.

## 4. Remove agent responsibilities from `fio-cli`

After `fio-ai` passes the clean-checkout verification:

1. Move, rather than copy, `AGENTS.AI.md`, `GAME.md`, and agent-only reference material to `fio-ai`.
2. Remove the two documentation submodules and `vendor/` from the CLI repository.
3. Simplify the CLI's root agent instructions to development/contributor guidance only.
4. Update the CLI README: `config.toml` lookup behavior and its scope as a CLI package. Link to `fio-ai` for agent workflows.
5. Update the AI README with bootstrap, `config.toml` setup, and supported `uv run fio` commands.

## Done when

- A clean `fio-cli` checkout contains no game-analysis instructions or third-party documentation submodules.
- A clean `fio-ai` checkout bootstraps the docs and pinned CLI with one command.
- `uv run fio ...` works from both repositories and selects that repository's `config.toml`, falling back to `~/.config/fio/config.toml` only when the project file is absent.
- Each dependency is reproducible from a Git tag plus committed lockfile.
