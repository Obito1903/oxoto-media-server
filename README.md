# Fabien's media server

## Introduction

This is a docker-compose file to run a media server with the following services:

- Plex Media Server | `http://plex.${DOMAIN_NAME}/web/`
- Sonarr (TV Shows & Anime downloader) | `http://sonarr.${DOMAIN_NAME}/`
- Radarr (Movies downloader) | `http://radarr.${DOMAIN_NAME}/`
- Prowlarr (Indexer for Sonarr & Radarr) | `http://prowlarr.${DOMAIN_NAME}/`
- qBittorrent (Torrent client) | `http://qbittorrent.${DOMAIN_NAME}/`
  - cyberghost (VPN)
- Traefik (Reverse proxy & SSL)
  - Error pages

## Installation

### 1. Clone the repository

```bash
git clone https://github.com/Obito1903/oxoto-media-server.git
```

### 2. Create the .env file

```bash
cp .env.example .env
```

### 3. Edit the .env file

Replace the values with your own. Mainly the following:

- `DOMAIN_NAME` : The domain name of your server (e.g. example.com)
- `DOWNLOADS_FOLDER` : The folder where your downloads will be stored
- `MOVIES_FOLDER` : The folder where your movies will be stored
- `TVSHOWS_FOLDER` : The folder where your TV shows will be stored
- `ANIMES_FOLDER` : The folder where your animes will be stored
- `CYBERGHOST_USERNAME` : Your Cyberghost username
- `CYBERGHOST_PASSWORD` : Your Cyberghost password
- `CYBERGHOST_COUNTRY` : The country you want to connect to (e.g. Netherlands)

```bash
code .env
```

### 4. Start the compose

```bash
docker-compose up -d
```

## Configuration

### 1. Plex Media Server

- Go to `http://plex.${DOMAIN_NAME}/web/`
- Sign in with your Plex account
- Add your libraries
- Enjoy

### 2. Qbittorrent

- Go to `http://qbittorrent.${DOMAIN_NAME}/`
- Sign in with the default credentials
  - Username: admin
  - Password: by default a random password is generated printed in the logs of the container (see below)
- Go to Settings > Web UI
- Change the default credentials

### 3. Prowlarr

- Go to `http://prowlarr.${DOMAIN_NAME}/`
- Create an account
- Go to Settings > Indexers
- Add flareSolverr as a solver
  - `+` > flareSolverr
    - Name: flareSolverr
    - Tags: cloudflare
    - Host: `http://flaresolverr:8191`
- Go to Indexers
  - Add New Indexer
  - Select your indexer
    - 1337x: General movies & tv torrents
    - cpasbien: French movies & tv torrents
    - rarbg: General movies & tv torrents
    - torrent9: French movies & tv torrents
    - nyaa.si: Anime torrents
    - anidex: Anime torrents
    - ...
  - Add tags (e.g. movies, tv, anime) to each indexer to be able to filter them later in Sonarr & Radarr

### 4. Sonarr

- Go to `http://sonarr.${DOMAIN_NAME}/`
- Add your Download Clients
  - `+` > qBittorrent
    - Name: qBittorrent
    - Host: qbittorrent
    - Port: 8080
    - Username: admin
    - Password: your qbittorrent password
    - Use SSL: No
- Add your media folders
  - Go to Settings > Media Management > Root Folders
  - Add Root Folder for TV Shows (/mnt/tvshows) & Anime (/mnt/animes)
- Go to Settings > Import Lists
  - Add the following lists:
    - Trakt Lists > Anime
    - Trakt Lists > TV Shows
- Go to Settings > General
  - Take note of the api key
- Go in Prowlarr
  - Go to Settings > Apps
  - Add Sonarr
    - Name: Sonarr
    - Tags: tv, anime
    - Prowlarr server: `http://prowlarr:9696`
    - Sonarr server: `http://sonarr:8989`
    - API Key: the api key you took note of earlier

### 5. Radarr

- Go to `http://radarr.${DOMAIN_NAME}/`
- Add your Download Clients
  - `+` > qBittorrent
    - Name: qBittorrent
    - Host: qbittorrent
    - Port: 8080
    - Username: admin
    - Password: your qbittorrent password
    - Use SSL: No
- Add your media folders
  - Go to Settings > Media Management > Root Folders
  - Add Root Folder for Movies (/mnt/movies)
- Go to Settings > Import Lists
  - Add the following lists:
    - Trakt Lists > Movies
- Go to Settings > General
  - Take note of the api key
- Go in Prowlarr
  - Go to Settings > Apps
  - Add Radarr
    - Name: Radarr
    - Tags: movies
    - Prowlarr server: `http://prowlarr:9696`
    - Radarr server: `http://radarr:7878`
    - API Key: the api key you took note of earlier
