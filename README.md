## 👋 Welcome to Lufi 🚀

Let's Upload that FIle - temporary file upload and sharing

## 📋 Description

Lufi (Let's Upload that FIle) is a temporary file upload and sharing service.
An uploader drops files into a share, the share gets a link, and that link
expires after the chosen delay. Nothing needs an account and no third party
scans the content, the files are served by your own host.

Uploads are stored in full on the host under the files volume and are removed
once the share expires. The database holding the share metadata lives in the
config volume, so both volumes need to be backed up together.

## 🚀 Services

- **app**: ghcr.io/megalis-bretagne/lufi:latest

## 📦 Installation

### Option 1: Quick Install
```bash
curl -q -LSsf "https://raw.githubusercontent.com/composemgr/lufi/main/docker-compose.yaml" -o compose.yml
```

### Option 2: Git Clone
```bash
git clone "https://github.com/composemgr/lufi" ~/.local/srv/docker/lufi
cd ~/.local/srv/docker/lufi
cp default.env .env
docker compose --env-file .env up -d
```

### Option 3: Using composemgr
```bash
composemgr install lufi
```

## 🔧 Configuration

### Environment Variables

```shell
TZ=America/New_York
LUFI_CONTACT=admin@example.com
APP_SECRET_KEY=replace-with-a-random-secret-min-32-chars
LUFI_MAX_FILE_SIZE=104857600
LUFI_DEFAULT_DELAY=0
LUFI_MAX_DELAY=0
```

See `docker-compose.yaml` for complete list of configurable options.

### Environment Files

`default.env.sample` holds the full set of variables and is copied to `.env`
before starting the stack:

```bash
cp default.env .env
docker compose --env-file .env up -d
```

`app.env.sample` holds the app-specific overrides and is copied to `app.env`:

```bash
cp app.env.sample app.env
```

`composemgr up` applies `app.env` and `default.env` automatically. Raw
`docker compose` commands need `--env-file .env` to pick up the same values.

### Reverse Proxy

Three standalone compose files ship in this repo:

| File | Use |
|------|-----|
| `docker-compose.yaml` | Default, published on `172.17.0.1:64241` |
| `docker-compose.traefik.yaml` | Behind an existing external `traefik` network |
| `docker-compose.tunnel.yaml` | Behind an existing external `cloudflare` tunnel network |

```bash
docker compose -f docker-compose.traefik.yaml --env-file .env up -d
```

Raise the body size limit in the proxy to match `LUFI_MAX_FILE_SIZE` if the
proxy rejects larger uploads.

## 🌐 Access

- **Web Interface**: http://172.17.0.1:64241

## 📂 Volumes

- `./volumes/config/lufi` - Lufi config and SQLite database
- `./volumes/data/lufi` - Uploaded files

## 🔐 Security

- Set `APP_SECRET_KEY` to a random value of at least 32 characters, changing it
  later makes links signed with the old key stop working
- Set `LUFI_CONTACT` to a real address, senders use it when a share expires
- Keep `LUFI_MAX_FILE_SIZE` and `LUFI_MAX_DELAY` at sane values, an
  unauthenticated uploader can otherwise fill the disk
- Use HTTPS via the reverse proxy in production, uploads travel in clear over
  plain HTTP
- The container is published only on the dockerhost address `172.17.0.1`
- Regularly update Docker images for security patches
- Backup your data regularly

## 🔍 Logging

```shell
docker compose --env-file .env logs -f app
```

## 🛠️ Management

```bash
# Start services
docker compose --env-file .env up -d

# Stop services
docker compose --env-file .env down

# Update to latest images
docker compose --env-file .env pull && docker compose --env-file .env up -d

# View logs
docker compose --env-file .env logs -f

# Restart services
docker compose --env-file .env restart
```

## 📋 Requirements

- Docker Engine 20.10+
- Docker Compose V2+

## 🤝 Author

🤖 casjay: [Github](https://github.com/casjay) 🤖  
🦄 composemgr: [Github](https://github.com/composemgr) 🦄
