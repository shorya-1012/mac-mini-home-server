# Storage

## Overview

Due to me being broke and AI eating up all the SSDs in the world, the home server currently uses the Mac mini's internal SSD for the
operating system, Docker configuration, application data, downloads,
and media.

The media server stores its data under:

```text
~/docker/jellyfin/data/
````

The storage layout separates application data from the actual media
library.

---

## Directory Structure

The current media-server directory is:

```text
docker/jellyfin/
│
├── compose.yml
│
├── gluetun/
├── jellyfin-cache/
├── jellyfin-config/
├── jellyseerr/
├── lidarr/
├── prowlarr/
├── qbittorrent/
├── radarr/
└── sonarr/
│
└── data/
    ├── downloads/
    │
    └── media/
        ├── movies/
        ├── music/
        └── shows/
```

The directories can be divided into two categories:

### Application Data

```text
gluetun/
jellyfin-cache/
jellyfin-config/
jellyseerr/
lidarr/
prowlarr/
qbittorrent/
radarr/
sonarr/
```

These directories contain persistent configuration, databases, and
other application state for the individual services.

### Media Data

```text
data/
├── downloads/
└── media/
    ├── movies/
    ├── music/
    └── shows/
```

This directory contains downloaded and organized media.

---

## Storage Layout

The storage layout can be summarized as:

```text
docker/jellyfin/
│
├── Application Data
│   │
│   ├── gluetun/
│   ├── jellyfin-cache/
│   ├── jellyfin-config/
│   ├── jellyseerr/
│   ├── lidarr/
│   ├── prowlarr/
│   ├── qbittorrent/
│   ├── radarr/
│   └── sonarr/
│
└── data/
    │
    ├── downloads/
    │
    └── media/
        ├── movies/
        ├── music/
        └── shows/
```

Application data and media are kept separate so that container
configuration can be managed independently from the actual media
library.

---

## Downloads

Temporary and incoming downloads are stored in:

```text
data/downloads/
```

qBittorrent uses this directory as its download location.

Files may remain in this directory while they are being downloaded or
while they are waiting to be imported by Radarr, Sonarr, or Lidarr.

The downloads directory is considered temporary working data rather
than the final media library.

---

## Media Library

Organized media is stored under:

```text
data/media/
```

The library is divided into three categories.

### Movies

```text
data/media/movies/
```

Movies are managed by Radarr and are made available to Jellyfin as the
movie library.

### Shows

```text
data/media/shows/
```

TV shows are managed by Sonarr and are made available to Jellyfin as the
TV library.

### Music

```text
data/media/music/
```

Music is managed by Lidarr and is made available to Jellyfin as the
music library.

---

## Media Data Flow

The general media flow is:

```text
                         qBittorrent
                              │
                              │ Download
                              ▼
                       /data/downloads
                              │
               ┌──────────────┼──────────────┐
               │              │              │
               ▼              ▼              ▼
            Radarr          Sonarr          Lidarr
               │              │              │
               │ Import       │ Import       │ Import
               ▼              ▼              ▼
            /movies/        /shows/        /music/
               │              │              │
               └──────────────┼──────────────┘
                              │
                              ▼
                           Jellyfin
                              │
                    ┌─────────┼─────────┐
                    │         │         │
                    ▼         ▼         ▼
                  Movies     Shows     Music
```

The services operate on the same underlying filesystem through the
shared `/data` mount.

---

## Docker Storage Mapping

The host's data directory is mounted into the containers as `/data`.

The Compose configuration uses:

```yaml
volumes:
  - ${DATA_LOCATION}:/data
```

The resulting mapping is:

```text
Host
~/docker/jellyfin/data/
        │
        │ Docker bind mount
        ▼
Container
/data/
├── downloads/
└── media/
    ├── movies/
    ├── music/
    └── shows/
```

This common path is important because qBittorrent, Radarr, Sonarr,
Lidarr, and Jellyfin can all access the same files.

---

## Application Configuration

Application configuration is stored separately from the media data.

```text
docker/jellyfin/
├── gluetun/
├── jellyfin-cache/
├── jellyfin-config/
├── jellyseerr/
├── lidarr/
├── prowlarr/
├── qbittorrent/
├── radarr/
└── sonarr/
```

These directories contain persistent application state and are mounted
into their respective containers.

For example:

```text
jellyfin-config/ → Jellyfin configuration
radarr/          → Radarr configuration/database
sonarr/          → Sonarr configuration/database
lidarr/          → Lidarr configuration/database
prowlarr/        → Prowlarr configuration/database
qbittorrent/     → qBittorrent configuration
jellyseerr/      → Jellyseerr configuration
gluetun/         → Gluetun configuration
```

This allows containers to be recreated without losing their
configuration.

---

## Storage Management

The server currently has limited internal storage, so disk usage should
be monitored regularly.

### Check filesystem usage

```bash
df -h
```

### Check the Docker deployment

```bash
du -sh ~/docker/jellyfin/*
```

### Check media and downloads

```bash
du -sh ~/docker/jellyfin/data/*
```

### Check individual media libraries

```bash
du -sh ~/docker/jellyfin/data/media/*
```

### Check Docker disk usage

```bash
docker system df
```
