<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/icon-dark.svg">
    <source media="(prefers-color-scheme: light)" srcset="assets/icon-light.svg">
    <img src="assets/icon.svg" alt="Open WebUI logo" width="128">
  </picture>
</p>

<h1 align="center">aac-openweb-ui</h1>

<p align="center">Self-hosted deployment definition for Open WebUI, a web interface for local and remote LLM backends.</p>

<p align="center">
  <a href="https://github.com/open-webui/open-webui"><img alt="Upstream" src="https://img.shields.io/badge/upstream-open--webui%2Fopen--webui-blue?logo=github"></a>
  <img alt="Image" src="https://img.shields.io/badge/image-ghcr.io%2Fopen--webui%2Fopen--webui%3Av0.12.5-2496ED?logo=docker&logoColor=white">
  <a href="https://github.com/open-webui/open-webui/blob/main/LICENSE"><img alt="License" src="https://img.shields.io/badge/license-Open%20WebUI%20License-green"></a>
</p>

## About

Open WebUI is a self-hosted, extensible web interface for large language models. It connects
to Ollama and to OpenAI-compatible APIs, and provides a chat interface with conversation
history, model selection, document and web-based retrieval, and tool calling. Accounts can
be managed locally or through an external OpenID Connect provider.

Key features (as provided by upstream):

- Chat interface for Ollama and OpenAI-compatible model backends
- Conversation history, prompt presets and model selection
- Retrieval-augmented generation with uploaded documents
- Tool and function calling
- Multi-user accounts with role management and OpenID Connect login (this deployment is prepared for Authentik)

This deployment also defines two optional dependencies: an Ollama model server (GPU) and a
Fish Speech text-to-speech server (GPU).

## Upstream

- Project: <https://github.com/open-webui/open-webui>
- Documentation: <https://docs.openwebui.com>
- Container image: `ghcr.io/open-webui/open-webui:v0.12.5`
- Dependencies: `ollama/ollama:0.40.13` (deployed as `aac-openweb-ui-ollama`), `fishaudio/fish-speech:v1.5.1` (deployed as `aac-openweb-ui-fish-speech`)
- License: Open WebUI License (upstream project)

## Quick start (Docker Compose)

The Compose file is rendered from `service.yml` by the App-as-Code template engine. For a
manual run, the external networks used for routing must exist first. They are attached by
the rendered deployment and are not defined in this repository:

```bash
# Create the external networks named in the rendered compose file, then:

# Provide the values listed under "Configuration" in .env / stack.env
docker compose up -d
```

Secrets are not stored in this repository. Provide them through the deployment pipeline
or create the `.env` and `stack.env` files yourself (see below). Do not commit them.

## Configuration

Environment variables (defaults from `service.yml`; `CHANGE_ME` must be set per deployment,
secrets are marked as such):

| Variable | Default | Description |
|---|---|---|
| `WEBUI_SECRET_KEY` | `CHANGE_ME` (secret) | Secret key used to sign authentication tokens |
| `WEBUI_URL` | `CHANGE_ME` | Public URL of the Open WebUI instance |
| `WEBUI_NAME` | organisation-specific | Display name shown in the UI |
| `CUSTOM_NAME` | organisation-specific | Custom instance name |
| `OPENAI_API_BASE_URL` | `CHANGE_ME` | Base URL of an OpenAI-compatible API |
| `OPENAI_API_KEY` | `CHANGE_ME` (secret) | API key for the OpenAI-compatible API |
| `OLLAMA_BASE_URL` | `CHANGE_ME` | Base URL of the Ollama server |
| `OLLAMA_GPU_DRIVER` | `nvidia` | GPU driver used by the Ollama dependency |
| `OLLAMA_DOCKER_TAG` | `latest` | Image tag for the Ollama dependency |
| `ENABLE_LOGIN_FORM` | `false` | Show the username/password login form |
| `ENABLE_OAUTH_SIGNUP` | `true` | Create accounts on first OpenID Connect login |
| `OAUTH_AUTO_REDIRECT` | `true` | Redirect directly to the OpenID Connect provider |
| `OAUTH_PROVIDER_NAME` | `authentik` | Label of the SSO login button |
| `OAUTH_CLIENT_ID` | `CHANGE_ME` | OpenID Connect client ID |
| `OAUTH_CLIENT_SECRET` | `CHANGE_ME` (secret) | OpenID Connect client secret |
| `OPENID_PROVIDER_URL` | `CHANGE_ME` | OpenID Connect discovery URL |
| `OPENID_REDIRECT_URI` | `CHANGE_ME` | OpenID Connect redirect URI |
| `OAUTH_SCOPES` | `openid email profile` | Requested OpenID Connect scopes |
| `OAUTH_MERGE_ACCOUNTS_BY_EMAIL` | `true` | Merge SSO logins with existing accounts that share an email address |
| `ENABLE_OAUTH_PERSISTENT_CONFIG` | `false` | Persist OpenID Connect settings in the database |
| `OPEN_TERMINAL_URL` | `CHANGE_ME` | URL of an external terminal service, if used |
| `TOOLS_FUNCTION_CALLING_ENABLED` | `true` | Enable tool and function calling |
| `API_SERVER_NAME` | `CHANGE_ME` | Server name of the Fish Speech dependency |
| `SERVICE_IMAGE_TAG` | `CHANGE_ME` | Image tag rendered into the deployment |
| `SERVICE_DOMAIN_NAME` | `CHANGE_ME` | Domain name used for routing |
| `PUID` / `PGID` | `1000` | User and group ID the container runs as |
| `TZ` | `Europe/Berlin` | Time zone |
| `PORT` | `8789` | Port the web interface listens on |

Ports:

| Port | Protocol | Purpose |
|---|---|---|
| `8789` | TCP | Web interface (`web`) |

Dependency ports (Fish Speech): `8080` TCP, API server.

Volumes:

| Mount | Description |
|---|---|
| `data` -> `/app/backend/data` | Persistent data for Open WebUI |
| `fish_speech_checkpoints` -> `/app/checkpoints` | Model weights for Fish Speech (dependency) |
| `fish_speech_references` -> `/app/references` | Reference audio files for Fish Speech (dependency) |
| `base` -> `/root/.ollama` | Persistent storage for Ollama models (dependency) |

## Deploy with App-as-Code

1. Edit `service.yml`. It is the single source of truth for image, ports, environment,
   volumes, dependencies and per-stage overrides. Do not edit the rendered output.
2. Pushing to `main` (or a configured stage branch) runs the service pipeline, which renders
   the manifests with `aac-template-engine` and triggers the deployment.
3. Releases of this definition are published under <https://github.com/app-as-code/aac-openweb-ui>.

## Credits and license

Open WebUI is developed by the Open WebUI project and licensed under the Open WebUI License.
That license includes branding restrictions: changing or removing "Open WebUI" branding is
only permitted within the terms stated in the upstream `LICENSE`. This repository only
contains the deployment definition for it and is not a fork of the application. The logo is
provided by the Open WebUI project; see [`assets/ICON_SOURCE.md`](assets/ICON_SOURCE.md)
for icon sources and licenses.
