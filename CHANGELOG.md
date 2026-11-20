# Changelog

All notable changes to this deployment definition are documented in this file.
The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]

### Changed

- Upstream image tracks Open WebUI v0.12.5 as defined in `service.yml`.
- Ollama dependency tracks version 0.40.13 as defined in `service.yml`.

## [0.0.1]

### Added

- Initial App-as-Code service definition for Open WebUI with Ollama and Fish Speech dependencies.
- Web interface on port 8789 with Traefik routing and automatic DNS.
- Optional OpenID Connect login (Authentik) alongside the login form.
- Container health check against the web interface.
- README with configuration reference and project icon.
