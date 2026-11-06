# Claude Code dev container

A ready-made [dev container](https://containers.dev/) for running [Claude Code](https://code.claude.com/docs) in an isolated Docker environment. A firewall blocks all outgoing traffic except the sites Claude Code and your project need, so Claude can work without asking permission for every command.

It is set up for Python projects managed with [uv](https://docs.astral.sh/uv/), such as those created from my `python-project-template` repository.

## What's inside

| File | Purpose |
|---|---|
| `.devcontainer/devcontainer.json` | Container settings: VS Code extensions, mounted folders, startup commands |
| `.devcontainer/Dockerfile` | The image: Node 22, Claude Code, uv, Python 3.13 and common dev tools |
| `.devcontainer/init-firewall.sh` | Blocks outgoing traffic except for the allowed sites |

## Requirements

- [Docker Desktop](https://www.docker.com/products/docker-desktop/), installed and running
- [VS Code](https://code.visualstudio.com/) with the [Dev Containers](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers) extension

## Add it to a project

1. Copy the `.devcontainer/` folder into the root of your project.
2. Add the sites your project's code needs to the firewall (see below).
3. Open the project in VS Code, open the Command Palette (`Cmd+Shift+P` / `Ctrl+Shift+P`) and run **Dev Containers: Reopen in Container**. The first build takes a few minutes.
4. In the VS Code terminal, run `claude` and sign in. If the browser sign-in doesn't return to the terminal, paste the code shown in the browser.

On the first build, the container runs `uv sync` and `uv run pre-commit install`, so the project expects a `pyproject.toml`.

## Allowing more sites

Add the domains your code calls to the `ALLOWED_DOMAINS` list in `.devcontainer/init-firewall.sh`:

```bash
    # Project-specific domains (APIs your code calls), for example:
    "data.snb.ch"
```

Then run **Dev Containers: Rebuild Container** so the change takes effect.

## Running without permission prompts

Inside the container you can start Claude Code with:

```bash
claude --dangerously-skip-permissions
```

The container limits what Claude can reach, but not everything:

- Claude can still change or delete any file in the project folder, so commit often.
- GitHub is reachable, so don't put SSH keys or a broad GitHub token in the container. Use a [fine-grained token](https://github.com/settings/personal-access-tokens) limited to the one repository.
- Only use it with repositories you trust.

## Good to know

- **Work on a project either in the container or on your computer, not both.** The `.venv` folder lives in the shared project folder, and the Linux and macOS/Windows versions would overwrite each other.
- **The firewall looks up IP addresses when the container starts.** If downloads fail after a long session, refresh it with `sudo /usr/local/bin/init-firewall.sh`.
- **Each project has its own Claude sign-in and settings.** To share one sign-in across all projects, remove `-${devcontainerId}` from the `claude-code-config` volume name in `devcontainer.json`.
- **Claude Code's optional telemetry is switched off,** since the firewall blocks it anyway.

## Credits

Adapted from Anthropic's [reference dev container](https://github.com/anthropics/claude-code/tree/main/.devcontainer), described in the [Claude Code documentation](https://code.claude.com/docs/en/devcontainer). Those files are © Anthropic PBC and subject to Anthropic's [Commercial Terms of Service](https://www.anthropic.com/legal/commercial-terms).
