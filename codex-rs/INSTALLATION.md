# Build and install the Linux CLI (Docker)

This guide shows how to package the Codex CLI as a Linux binary using Docker and install it on Debian/Ubuntu hosts or from inside a running container.

## Package the binary with Docker (glibc)

```powershell
cd G:\Coding_Workspace\codex\codex-rs
docker build -t codex-cli:linux -f Dockerfile .

$id = docker create codex-cli:linux
docker cp "${id}:/usr/local/bin/codex" .\codex-linux-amd64
docker rm $id
```

Optional: export directly from the builder stage without creating a container:

```powershell
docker buildx build --target builder --output type=local,dest=.\dist -f Dockerfile .
copy .\dist\target\release\codex .\codex-linux-amd64
```

## Install globally on a Debian/Ubuntu host

```bash
chmod +x codex-linux-amd64
sudo install -m 0755 codex-linux-amd64 /usr/local/bin/codex
codex --help
```

## Install from inside a running Debian/Ubuntu container

Start the container with your host folder mounted so the file is available at `/mnt`:

```powershell
docker run --rm -it -v "G:\\Coding_Workspace\\codex\\codex-rs:/mnt" debian:bookworm-slim bash
```

Inside the container, install runtime deps and place the binary on PATH:

```bash
apt-get update && apt-get install -y ca-certificates libssl3
install -m 0755 /mnt/codex-linux-amd64 /usr/local/bin/codex
codex --help
```

### Notes
- The extracted binary is glibc-linked. If you see a glibc version error on older bases, use a newer base (e.g., `ubuntu:24.04`) or build a MUSL static binary instead.
- `/usr/local/bin` is on PATH by default on Debian/Ubuntu.


docker run --rm -it -v "G:\Coding_Workspace\codex\codex-rs:/mnt" ubuntu:24.04 bash

