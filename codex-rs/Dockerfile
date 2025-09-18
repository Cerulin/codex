# Dockerfile
FROM rust:slim AS builder
RUN apt-get update && apt-get install -y --no-install-recommends \
    pkg-config build-essential ca-certificates libssl-dev && rm -rf /var/lib/apt/lists/*
WORKDIR /app
# Copy the Rust workspace
COPY . .
# Build just the CLI binary (release)
RUN cargo build -p codex-cli --bin codex --release

FROM debian:bookworm-slim
RUN apt-get update && apt-get install -y --no-install-recommends \
    ca-certificates libssl3 && rm -rf /var/lib/apt/lists/*
COPY --from=builder /app/target/release/codex /usr/local/bin/codex
WORKDIR /work
ENTRYPOINT ["codex"]