# rifuki-deploy

Public release channel for the `rifuki-deploy` CLI.

`rifuki-deploy` is a small deployment helper for rifuki.dev infrastructure. It generates production config, validates the project setup, checks service health, and wraps common Docker Compose operations.

## Install

Recommended install for Linux x86_64:

```bash
curl -fsSL https://github.com/rifuki/rifuki-deploy/releases/download/latest-deploy/install.sh | bash
```

The installer downloads the `rifuki-deploy` binary from this repository's `latest-deploy` release and installs it to `~/.local/bin`. Make sure `~/.local/bin` is in your `PATH`.

Inspect the installer before running it:

```bash
curl -fsSL https://github.com/rifuki/rifuki-deploy/releases/download/latest-deploy/install.sh
```

Manual install:

```bash
curl -L -o rifuki-deploy https://github.com/rifuki/rifuki-deploy/releases/download/latest-deploy/rifuki-deploy
chmod +x rifuki-deploy
mkdir -p ~/.local/bin
mv rifuki-deploy ~/.local/bin/
```

## Quick Start

Clone or keep the site repo under the VPS app convention:

```bash
mkdir -p ~/apps
cd ~/apps/rifuki.dev
```

Run the interactive setup:

```bash
rifuki-deploy init
```

Then validate and start the stack:

```bash
rifuki-deploy doctor
docker compose up -d
rifuki-deploy health --production --domain rifuki.dev
```

When started outside the project, `rifuki-deploy` looks for the project in this order:

1. Current directory
2. Parent directory
3. `RIFUKI_DEV_DIR`
4. `~/apps/rifuki.dev`
5. `~/app/rifuki.dev`
6. `~/rifuki.dev`
7. `~/mgodonf/web2/rifuki.dev`

If the selected path is empty or missing, the CLI can clone `rifuki/rifuki.dev` for you. That repository is private, so the VPS must already be authenticated with GitHub and have access to the repo.

## What It Does

- Generates production config files for `rifuki-server` and `miku-agent`
- Builds a `docker-compose.override.yml` for deployment-specific overrides
- Supports full VPS deployment or web-on-Vercel deployment
- Configures Last.fm credentials when needed
- Configures OpenRouter, Groq, and Ollama provider credentials
- Checks local and production service health
- Provides shortcuts for Docker Compose status, logs, and restarts

## Commands

| Command | Purpose |
| --- | --- |
| `rifuki-deploy` | Run interactive setup. Same as `rifuki-deploy init`. |
| `rifuki-deploy init` | Generate deployment config with guided prompts. |
| `rifuki-deploy update` | Download and install the latest CLI binary from the public release. |
| `rifuki-deploy doctor` | Validate project files, generated config, Docker, and service setup. |
| `rifuki-deploy health` | Check local services and AI provider availability. |
| `rifuki-deploy health --production --domain rifuki.dev` | Check production web, API, and agent endpoints. |
| `rifuki-deploy status` | Show `docker compose ps`. |
| `rifuki-deploy restart [service]` | Restart all services or one service. |
| `rifuki-deploy logs <service>` | Show recent service logs. |
| `rifuki-deploy logs <service> -l 100 -f` | Show 100 lines and follow logs. |
| `rifuki-deploy config` | Print generated config with secrets masked. |
| `rifuki-deploy config --show-secrets` | Print generated config including secret files. |

Supported services for restart/logs are `rifuki-server`, `miku-agent`, `rifuki-www`, or `all` where applicable.

## Generated Files

`rifuki-deploy init` writes deployment-specific files into the project:

| File | Purpose |
| --- | --- |
| `rifuki-server/config/local.toml` | Server runtime overrides. |
| `rifuki-server/config/secret.toml` | Last.fm credentials. |
| `miku-agent/config/local.toml` | Agent runtime overrides and provider priority. |
| `miku-agent/config/secret.toml` | AI provider API keys. |
| `docker-compose.override.yml` | Production Docker Compose overrides. |

## Release Assets

`latest-deploy` is refreshed from the latest successful release workflow. Versioned releases use tags like `deploy-v1.2.3`.

Each release publishes:

- `rifuki-deploy` - Linux x86_64 static binary
- `install.sh` - installer that downloads the matching binary

## Requirements

- Linux x86_64 target host
- `curl` for installation
- Docker and Docker Compose for deployment commands
- A checked-out `rifuki.dev` project for `init`, `doctor`, and Docker operations
