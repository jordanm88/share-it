# <div align="center"><img  src="https://user-images.githubusercontent.com/58886915/166198400-c2134044-1198-4647-a8b6-da9c4a204c68.svg" width="40"/> </br>Pingvin Share</div>


This app is an adaptation of Pingvin Share, which is a self-hosted file sharing platform and an alternative for WeTransfer.

## ✨ Features

- You can share files using a link
- Unlimited file size (restricted only by disk space)
- Set an expiration date for shares
- Secure shares with visitor limits and passwords
- Email recipients
- Integration with ClamAV for security scans

## ⌨️ Setup

> Note: This app is in its early stages and may contain bugs.

### Installation with Docker (recommended)

1. Download the `docker-compose.yml` file
2. Run `docker compose up -d`

The website is now listening on `http://localhost:3000`, have fun!

### Stand-alone Installation

Required tools:

- [Node.js](https://nodejs.org/en/download/) >= 16
- [Git](https://git-scm.com/downloads)
- [pm2](https://pm2.keymetrics.io/) for running Pingvin Share in the background

```bash
git clone https://github.com/jordanm88/share-it
cd share-it

# Checkout the latest version
git fetch --tags && git checkout $(git describe --tags `git rev-list --tags --max-count=1`)

# Start the backend
cd backend
npm install
npm run build
pm2 start --name="share-it-backend" npm -- run prod

# Start the frontend
cd ../frontend
npm install
npm run build
API_URL=http://localhost:8080 # Set the URL of the backend, default: http://localhost:8080
pm2 start --name="share-it-frontend" .next/standalone/server.js
```

**Uploading Large Files**: By default, this app uses a built-in reverse proxy to reduce the installation steps. However, this reverse proxy is not optimized for uploading large files. 

If you wish to upload larger files, you can either use the Docker installation or set up your own reverse proxy. An example configuration for Caddy can be found in `./Caddyfile`.

The website is now listening on `http://localhost:3000`, have fun!

### Integrations

#### ClamAV (Docker only)

ClamAV is used to scan shares for malicious files and remove them if found.

1. Add the ClamAV container to the Docker Compose stack (see `docker-compose.yml`) and start the container.
2. Docker will wait for ClamAV to start before starting Pingvin Share. This may take a minute or two.
3. The app logs should now log "ClamAV is active"

Please note that ClamAV needs a lot of [ressources](https://docs.clamav.net/manual/Installing/Docker.html#memory-ram-requirements).

#### OAuth 2 Login

View the [OAuth 2 guide](/docs/oauth2-guide.md) for more information.

### Upgrade to a new version

As the app is in an early stage, see the release notes for breaking changes before upgrading.

#### Docker

```bash
docker compose pull
docker compose up -d
```

#### Stand-alone

1. Stop the running app
   ```bash
   pm2 stop share-it-backend share-it-frontend
   ```
2. Repeat the steps from the [installation guide](#stand-alone-installation) except the `git clone` step.

   ```bash
   cd share-it

   # Checkout the latest version
   git fetch --tags && git checkout $(git describe --tags `git rev-list --tags --max-count=1`)

   # Start the backend
   cd backend
   npm install
   npm run build
   pm2 restart share-it-backend

   # Start the frontend
   cd ../frontend
   npm install
   npm run build
   API_URL=http://localhost:8080 # Set the URL of the backend, default: http://localhost:8080
   pm2 restart share-it-frontend
   ```

### Configuration

You can customise the app, like changing your domain by going to the configuration page in your admin dashboard `/admin/config`.

#### Environment variables

For installation specific configuration, you can use environment variables. The following variables are available:

##### Backend

| Variable         | Default Value                                      | Description                            |
| ---------------- | -------------------------------------------------- | -------------------------------------- |
| `PORT`           | `8080`                                             | The port on which the backend listens. |
| `DATABASE_URL`   | `file:../data/share-it.db?connection_limit=1` | The URL of the SQLite database.        |
| `DATA_DIRECTORY` | `./data`                                           | The directory where data is stored.    |
| `CLAMAV_HOST`    | `127.0.0.1`                                        | The IP address of the ClamAV server.   |
| `CLAMAV_PORT`    | `3310`                                             | The port number of the ClamAV server.  |

##### Frontend

| Variable  | Default Value           | Description                              |
| --------- | ----------------------- | ---------------------------------------- |
| `PORT`    | `3000`                  | The port on which the frontend listens.  |
| `API_URL` | `http://localhost:8080` | The URL of the backend for the frontend. |

## 🖤 Contribute

### Project

You're very welcome to contribute to this app or, the original Pingvin Share!
