# The engine the daily run uses, and the one place this repository writes its
# digest: validate.yml reads it from here and fails if daily.yml's pinned engine
# runs a different one.
FROM ghcr.io/ghurault/llm-curated-rss-engine@sha256:d47492f758eec62ace8e2fc74866688a33638ce69db32feeadb391013bc50c33

# The VS Code server needs libatomic1 on a slim base; the rest is for git.
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        git \
        less \
        libatomic1 \
        openssh-client \
        procps \
    && rm -rf /var/lib/apt/lists/*
