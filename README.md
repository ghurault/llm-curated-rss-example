# LLM Curated RSS Feeds — example configuration

A minimal, working configuration repository for the [engine](https://github.com/ghurault/llm-curated-rss-engine): one feed, `general`, built from BBC, CNN and Le Monde.
Copy it, replace the placeholders listed below, and it is yours.

The code that fetches, scores, selects and publishes is the engine, and this repository only runs it at a pinned commit.
How the pipeline works, its commands and its privacy model are all in the [engine's README](https://github.com/ghurault/llm-curated-rss-engine/blob/main/README.md).

> **Make your copy private.**
> A configuration repository holds subscription lists and, above all, an editorial policy, which says directly what you care about.

> **Nothing here runs on its own.**
> Both workflows are dispatch-only, and the feed's `[deploy] provider` is `"none"`, so even a run started by hand builds the feed and uploads nothing.
> Turning it on is steps 4 to 6 below.

## The feed

| feed      | what it is for                                                                                             |
| --------- | ---------------------------------------------------------------------------------------------------------- |
| `general` | the handful of stories a reader would regret missing, plus two topics it follows on purpose: AI and the UN |

A feed is a directory under `feeds/` holding its `config.toml`, `editorial-policy.md` and `sources.opml`, and is run independently of any other.

## Layout

```text
├── feeds/<name>/                one runner per curated feed
├── eval/corpus/<name>/          collected items (ignored)
├── eval/labels/<name>/          notes on how good the picks were (ignored)
├── state/<name>/                what each stage produced (ignored)
├── build/<name>/                the feed, ready to upload (ignored)
├── .devcontainer/               the pinned engine image, plus git
└── .github/workflows/
    ├── daily.yml                the schedule, the matrix and the credentials
    └── validate.yml             checks the runners and the wiring around them
```

## What to change before it is yours

1. **The subscriptions** — replace [`feeds/general/sources.opml`](feeds/general/sources.opml) with the OPML your feed reader exports, and commit it: CI reads the list directly.
2. **The policy** — [`feeds/general/editorial-policy.md`](feeds/general/editorial-policy.md) is the whole product. Its topics, exclusions and penalties are examples, not defaults.
3. **The contact URL** in `[fetch] user_agent`, so feed operators have somewhere to complain to.
4. **The feed's URL** — `[publish] site_url` and `[deploy] project` are placeholders, and `provider` is `"none"`. The engine's [SETUP documentation](https://github.com/ghurault/llm-curated-rss-engine/blob/main/docs/SETUP.md) explains how to generate a name and why it must not be descriptive. The URL is baked into the feed, so changing it later makes every reader treat every item as new.
5. **The secrets** — `ANTHROPIC_API_KEY`, `CLOUDFLARE_API_TOKEN` and `CLOUDFLARE_ACCOUNT_ID`, as repository secrets. Nothing else the run needs is outside the repository.
6. **The triggers** — uncomment the `schedule:` block in [`daily.yml`](.github/workflows/daily.yml) and the `pull_request:` and `push:` blocks in [`validate.yml`](.github/workflows/validate.yml). Do this last: every unattended run spends model credits.

Prove the feed in a reader with the `stub` scorer, which spends nothing, before the schedule starts paying for it.

## Working locally

Open the repository in the devcontainer (_Dev Containers: Reopen in Container_).
It is built from the same engine image CI uses, so every `curate-*` command is on `PATH` at the version CI runs.
The container reads `.env` at the repository root, which must exist — `touch .env` before reopening — and which is where `ANTHROPIC_API_KEY` goes if you want to score by hand.

There is no root `config.toml`, so every command names its runner:

```bash
curate-validate feeds/
curate-fetch --config feeds/general/config.toml
curate-run --config feeds/general/config.toml --date 2026-08-08 --provider stub
```

`curate-run` stops at `build/`; publishing is CI's.
Tuning a policy without an API key is described in the engine's [README](https://github.com/ghurault/llm-curated-rss-engine/blob/main/README.md#tuning-the-policy).

## Upgrading the engine

The engine is pinned twice, and both pins move together:

- the commit in `daily.yml`'s `uses:` line, which decides what the daily run executes;
- the image digest in `.devcontainer/Dockerfile`, which decides what runs locally and in `validate.yml`.

1. Choose an engine commit on `main`, and copy the `image:` line from its `curate.yml`:

   ```bash
   curl -s https://raw.githubusercontent.com/ghurault/llm-curated-rss-engine/<commit>/.github/workflows/curate.yml | grep image:
   ```

2. In one pull request, put the commit in `daily.yml` and the image in the `FROM` line of `.devcontainer/Dockerfile`.
   `validate.yml` fails until the two agree.
3. After merging, rebuild the devcontainer.

Read the engine's history since the pinned commit first: a change to `curate.yml`'s inputs, secret names or cache paths needs a matching change here, and a moved cache path loses the corpus and the published log without an error.

## Adding a feed

1. Create `feeds/<name>/` from the engine's references, replacing every `<name>` in the config:

   ```bash
   mkdir -p feeds/<name>
   curate-template config > feeds/<name>/config.toml
   curate-template policy > feeds/<name>/editorial-policy.md
   ```

2. Export its `sources.opml` from the feed reader, and write the policy.
3. Give it its own Worker name and URL. They must not be another feed's: an upload replaces a Worker's whole asset manifest.
4. Add `<name>` to the matrix in `daily.yml` and to the `feed` dispatch input's options. `validate.yml` checks the matrix, not the options.
5. Run `curate-validate feeds/`.

The secrets are shared by every feed, so nothing else has to change.

## Reference

- [Engine README](https://github.com/ghurault/llm-curated-rss-engine/blob/main/README.md): the pipeline, privacy, commands and flags.
- [Engine SETUP](https://github.com/ghurault/llm-curated-rss-engine/blob/main/docs/SETUP.md): detailed explanation on how to get your instance running.
- [Engine ARCHITECTURE](https://github.com/ghurault/llm-curated-rss-engine/blob/main/docs/ARCHITECTURE.md): why it is shaped this way.
- [Engine scoring spec](https://github.com/ghurault/llm-curated-rss-engine/blob/main/docs/scoring-spec.md): how judgements become a ranking, which the `[policy]` settings tune.
