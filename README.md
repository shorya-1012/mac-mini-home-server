# Home Server

A self-hosted home server running **Debian 13** on a 2012 Mac mini, serving a fully automated media stack with **Jellyfin**, the *arr apps, and a VPN-routed download pipeline, all managed with Docker Compose.

> **Work in progress.** This is a living setup that I keep changing and improving, and the docs track it as it goes.

## Highlights

- **Media streaming** with Jellyfin
- **Request-driven automation**: request a movie or show in Jellyseerr, and Radarr, Sonarr, Prowlarr, and qBittorrent do the rest
- **VPN-isolated downloads**: all download and indexer traffic goes through Gluetun (ProtonVPN)
- **Persistent bind mounts**: containers can be recreated without losing configuration

## Hardware

| Component | Details                                   |
| --------- | ----------------------------------------- |
| Machine   | Mac mini (Late 2012), `Macmini6,1`        |
| CPU       | Intel Core i5, 2.5 GHz, 2 cores / 4 threads |
| RAM       | 8 GB                                      |
| Storage   | 128 GB internal SSD                       |
| Network   | Wi-Fi                                     |

## Software

| Layer          | Choice                  |
| -------------- | ----------------------- |
| OS             | Debian 13               |
| Init system    | systemd                 |
| Filesystem     | btrfs                   |
| Containers     | Docker Engine + Compose |

## Services

All services run from [`~/docker/jellyfin/compose.yml`](./docs/07_media_server.md).

| Service      | Purpose                       |
| ------------ | ----------------------------- |
| Jellyfin     | Media streaming               |
| Jellyseerr   | Media requests                |
| Radarr       | Movie management              |
| Sonarr       | TV show management            |
| Lidarr       | Music management              |
| Prowlarr     | Indexer management            |
| qBittorrent  | Download client               |
| Gluetun      | VPN gateway                   |
| FlareSolverr | Cloudflare challenge handling |

## Documentation

| Document                                  | Description                                              |
| ----------------------------------------- | -------------------------------------------------------- |
| [Hardware](./docs/01_hardware.md)         | Docker installation, Compose usage, networking, updates  |
| [Installation](./docs/02_installation.md) | Docker installation, Compose usage, networking, updates  |
| [Network](./docs/03_network.md)           | Docker installation, Compose usage, networking, updates  |
| [SSH](./docs/04_ssh.md)                   | Docker installation, Compose usage, networking, updates  |
| [Docker](./docs/05_docker.md)             | Docker installation, Compose usage, networking, updates  |
| [Storage](./docs/06_storage.md)           | Directory layout, data flow, and disk usage              |
| [Media Server](./docs/07_media_server.md) | Every service in the stack, plus how requests flow       |

## Status

### Base System
- [x] Debian installed
- [x] SSH configured
- [x] Stable LAN IP configured
- [x] Automatic security updates (unattended-upgrades)
- [ ] Firewall (ufw/nftables)
- [ ] Fail2ban / SSH hardening
- [ ] Time sync (systemd-timesyncd or chrony)

### Containers and Media
- [x] Docker Engine and Compose installed
- [x] Jellyfin, Jellyseerr, and the *arr stack deployed
- [x] VPN-routed downloads via Gluetun
- [ ] Verify Jellyfin hardware transcoding (`/dev/dri`)
- [ ] Pin container image versions
- [ ] Backups for application configuration

### Storage
- [ ] Move media and downloads to external or larger storage

## Known Limitations

- **Limited storage.** The 128 GB internal SSD holds the OS, Docker data, downloads, and media, so disk usage needs regular monitoring (see [Storage](./docs/storage.md)).
- **Wi-Fi only.** Streaming performance depends on wireless signal quality.
- **Modest CPU.** Software transcoding on a 2-core CPU is limited, so direct play or hardware acceleration is preferred.
