# RxFilms server

The home media server behind RxFilms, part of [rxenv](https://github.com/RexSAS/rxenv).

## Stack

| Area | What runs |
|---|---|
| Hardware | Small Intel Haswell box (i3, HD 4400 iGPU) |
| OS | Ubuntu 24.04 |
| Media | Jellyfin (native package, jellyfin-ffmpeg), VA-API hardware transcoding |
| Requests & library | Seerr, Sonarr, Radarr, Prowlarr, FlareSolverr, qBittorrent (Docker + Watchtower) |
| Storage | SSD for OS/metadata/transcode cache, USB HDDs pooled with mergerfs |
| Remote access | Cloudflare (proxied, IP whitelist) → nginx (HTTP/2, TLS 1.2/1.3, origin cert) → Jellyfin; WireGuard for LAN access from outside |

## Tuning applied

- **Transcoding**: VA-API on for H.264/MPEG-2/VC-1, throttling and segment deletion on; prefer H.264 releases since the iGPU can't help with HEVC/HDR.
- **Network**: `fq` + BBR, larger socket buffers; Jellyfin knows its proxies and LAN ranges, so home clients stream at full quality.
- **Memory**: `vm.swappiness = 10`.
- **Storage**: `noatime` on media disks, mergerfs `cache.files=partial,dropcacheonclose=true`, SMART monitoring on.
- **nginx**: real client IP from Cloudflare, long WebSocket timeout for `/socket`, upstream on localhost only.

## Clients

- [RxFilms for FireStick and LG TV](https://github.com/RexSAS/RxFilms-Tv-AppInstaller)
- [rxhome-tv](https://github.com/RexSAS/rxhome-tv) launcher
