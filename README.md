# Nginx Proxy Manager + GoAccess (Docker Stack) for Raspberry Pi 4 & 5

This stack runs **Nginx Proxy Manager (NPM)** for simple TLS reverse proxying and **GoAccess** for live proxy log charts. Configuration is driven by an environment file you create by renaming `env.txt` to `.env`.

---

## What’s included

- **Nginx Proxy Manager**
  - Publishes ports **80**, **81** (admin UI), and **443** on the host
  - Persists config and certificates on a named volume
- **GoAccess for Nginx Proxy Manager**
  - Publishes port **7880** (HTML dashboard) on the host
  - Reads NPM proxy logs and renders a real‑time report
  
See port mappings, volumes, and networks in `docker-compose.yml`.

---

## Quick start

### 1) Clone and enter the repo
```bash
git clone https://github.com/iamjavadali/nginx-reverse-proxy.git
cd nginx-reverse-proxy
```

### 2) Rename `env.txt` to `.env` and edit values
```bash
mv env.txt .env
nano .env
```

**Minimum changes in `.env`:**  
- `TZ` → your timezone, e.g. `America/New_York`  
- `BASIC_AUTH=True` to protect the GoAccess dashboard (recommended)  
- `BASIC_AUTH_USERNAME` and `BASIC_AUTH_PASSWORD` → set your own credentials  
- Optional tuning: `HTML_REFRESH`, `KEEP_LAST`, `PROCESSING_THREADS`, `DEBUG`, `SKIP_ARCHIVED_LOGS`  
Variables are documented in the provided `env.txt`.

> Note: the GoAccess image documents `BASIC_AUTH`, `BASIC_AUTH_USERNAME`, and `BASIC_AUTH_PASSWORD` as standard environment variables. If you prefer using a Docker secret file, pass `BASIC_AUTH_PASSWORD_FILE` in your compose; otherwise pass `BASIC_AUTH_PASSWORD`.

### 3) Start the stack
```bash
docker compose up -d
```
This launches:
- **NPM** on host ports **80/443** and the **admin UI on 81**
- **GoAccess** on host port **7880**  
Confirm via the compose port mappings. 

---

## Access and initial setup

### Nginx Proxy Manager
Open the admin UI at:
```
http://<your-host>:81/
```
Default first‑login credentials (change immediately):
- **Email:** `admin@example.com`
- **Password:** `changeme` 

You’ll be prompted to update the admin details on first sign‑in. The official setup guide covers adding proxy hosts and enabling free Let’s Encrypt certificates.

### GoAccess dashboard
Open:
```
http://<your-host>:7880/
```
If `BASIC_AUTH=True`, you’ll be asked for the username/password you set in `.env`. The GoAccess image reads NPM logs from the mounted volume and renders a live HTML dashboard; see the image README and GoAccess manual for behavior and tuning flags like refresh interval and days kept. 

---

## Data, ports, and networks (from `docker-compose.yml`)

- **Ports**
  - NPM: `80:80`, `81:81`, `443:443`
  - GoAccess: `7880:7880`  
- **Volumes**
  - `npm_data` bound to `/data`
  - Let’s Encrypt data mapped at `/etc/letsencrypt`
  - NPM logs mounted read‑only into GoAccess at `/opt/log`
  - `goaccess_data` for the GoAccess database/output
- **Networks**
  - Both services join the `proxy` network (a `nextcloud` network may also be defined for separate stacks).  
All of the above are defined directly in this compose file.

---

## Environment reference (from `env.txt`)

The `.env` file controls GoAccess. The template includes:

```dotenv
# Timezone
TZ=America/New_York

# Log parser behavior
LOG_TYPE=NPM
SKIP_ARCHIVED_LOGS=False
DEBUG=False
HTML_REFRESH=5
KEEP_LAST=30
PROCESSING_THREADS=1

# Dashboard basic auth
BASIC_AUTH=True
BASIC_AUTH_USERNAME=admin
BASIC_AUTH_PASSWORD=Password123$
```
Edit each value to suit your environment before starting the stack.

---

## Common tasks

```bash
# Tail logs
docker compose logs -f npm
docker compose logs -f goaccess

# Restart services
docker compose restart npm goaccess

# Stop and remove containers (keeps volumes)
docker compose down
```

---

## Security notes

- Change the default NPM admin credentials on first login.
- Keep port **81** reachable only to trusted networks; only 80/443 need public exposure for proxying.
- Protect the GoAccess dashboard with basic auth and strong credentials, or restrict by IP.
- Back up the NPM data volume (config and certs) and the GoAccess data volume regularly.

---

## References

- **Nginx Proxy Manager**: official setup and first‑login docs.
- **GoAccess for NPM image**: environment variables and usage.
- **GoAccess manual**: real‑time HTML report details.
