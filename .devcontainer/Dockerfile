# The engine the daily run uses, and the one place this repository writes its
# digest: validate.yml reads it from here and fails if daily.yml's pinned engine
# runs a different one.
FROM ghcr.io/ghurault/llm-curated-rss-engine@sha256:3a1cedc33ce90c422ece6da9f7a9648e67c12b36bec5a2cecf6b10b0d20f375d

# The VS Code server needs libatomic1 on a slim base; the rest is for git.
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        git \
        less \
        libatomic1 \
        openssh-client \
        procps \
    && rm -rf /var/lib/apt/lists/*
