# Contributing

Thanks for helping improve this Nginx Proxy Manager + GoAccess stack. This repo provides a Docker Compose setup for NPM (reverse proxy + TLS) and GoAccess (live charts of proxy logs).

Before contributing, please read the [Code of Conduct](CODE_OF_CONDUCT.md).

## Repo layout
- `docker-compose.yml` — services, ports, volumes, networks
- `env.txt` — copy/rename to `.env` and edit values before starting
- `README.md` — usage and setup guide

## Getting started (local)
1. **Prereqs:** Docker and the Docker Compose plugin installed.
2. **Environment:** rename `env.txt` → `.env` and update values (timezone, GoAccess auth, etc.). Do **not** commit `.env`.
3. **Start stack:** `docker compose up -d`
4. **Access:**
   - NPM admin UI: `http://<host>:81/` (change default admin credentials on first login)
   - GoAccess dashboard: `http://<host>:7880/` (use Basic Auth if enabled in `.env`)

## Proposing changes
- For significant changes (ports, volume layout, defaults), open an **issue** first.
- Branch from `main`, keep commits focused, and update docs when behavior changes.
- If you add or change environment variables, update `env.txt` and the README.

## Commit style
Use concise, imperative messages, e.g.:
- `compose: mount logs read-only for goaccess`
- `docs: add reverse proxy hardening notes`

## Pull request checklist
- [ ] `docker compose config` passes
- [ ] Stack starts locally (`docker compose up -d`)
- [ ] README and `env.txt` updated as needed
- [ ] No secrets or `.env` committed
- [ ] CI (if present) passes

## Security
Do **not** disclose vulnerabilities in public issues. See [SECURITY.md](SECURITY.md).

## License
By contributing, you agree your contributions are licensed under the terms in `LICENSE` (MIT).
