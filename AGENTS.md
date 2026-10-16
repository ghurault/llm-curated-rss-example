# Agent instructions

The conventions for this repository, whichever assistant is being used.

## Role & Project Context

This is an example configuration repository for a set of LLM-curated RSS feeds.
It holds no code: each feed is a directory under `feeds/` (a TOML config, a Markdown editorial policy and an OPML subscription list), and two GitHub Actions workflows run the public [engine](https://github.com/ghurault/llm-curated-rss-engine) against them.
How the engine behaves, and why, is documented in that repository, not this one.

- Prioritize readability and correctness over cleverness.
- When proposing a solution, clarify the pros and cons of different approaches, and consider the strongest case against your recommendation.
- Be honest, always state when you are unsure, and ask for clarification if needed.
- Unless stated otherwise, be concise in your answers, comments and documentation.

## 1. Project Constraints

- **There is no root `config.toml`**: every command takes `--config feeds/<name>/config.toml`, or is run from inside a runner's directory. A runner is its directory, and its name is what the workflow's matrix carries.
- **Runners share nothing they write**: no two runners may share a state directory, corpus, build directory, Worker or feed URL. `curate-validate feeds/` checks this.
- **The engine is pinned, never `latest`**: by commit in `daily.yml` and by image digest in `.devcontainer/Dockerfile`, and the two move together in one pull request. An unattended run must not change behaviour because someone pushed to the engine.
- **Only CI publishes**: `daily.yml` is the only thing that uploads a feed. Never pass `--deploy` by hand; the devcontainer holds no deploy credential for exactly that reason.
- **Credentials come from the environment, never from config**: a setting that holds a token is a bug, not a convenience. CI's arrive as repository secrets, named one by one in `daily.yml`.

## 2. Workflow & Tooling

- **Environment**: All commands run inside the devcontainer, which is the engine image CI uses. It has `git` but no `make`.
- **Commits**: Use the Conventional Commits format. Nothing enforces it, so it is up to whoever writes the message.
- **Checks**: `curate-validate feeds/` after any edit to a runner. Workflow YAML is not linted: a workflow change is only verified by running it.

## 3. Formatting

Formatters per language are configured in `.vscode/settings.json`; write files so they need no reformatting once that formatter runs.

- **JSON / JSONC / YAML**: Formatted with Prettier.
- **Markdown**: Formatted with Prettier.
  - _Line breaks_: Never break a line in the middle of a sentence. Prefer to write each sentence (or clause, if long) on its own line.
- **TOML**: Formatted with the `even-better-toml` extension (`taplo`), at its defaults.
