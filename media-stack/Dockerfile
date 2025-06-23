version: '3.8'

services:

  qbittorrent:
    image: lscr.io/linuxserver/qbittorrent:latest
    container_name: qbittorrent
    environment:
      - PUID=0
      - PGID=0
      - TZ=Europe/Stockholm
      - WEBUI_PORT=8080
    volumes:
      - /mnt/.docker/qbittorrent/config:/config
      - type: bind
        source: /mnt/nfs-media/downloads
        target: /downloads
        bind:
          propagation: rshared
    restart: unless-stopped
    networks:
      - shared-net
### Books ###
  readarr:
    image: lscr.io/linuxserver/readarr:develop
    container_name: readarr
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/Stockholm
    volumes:
      - /mnt/.docker/readarr/config:/config
      - type: bind
        source: /mnt/nfs-media/books
        target: /books
        bind:
          propagation: rshared
      - type: bind
        source: /mnt/nfs-media/downloads
        target: /downloads
        bind:
          propagation: rshared
    restart: unless-stopped
    networks:
      - shared-net
  calibre-web:
    image: linuxserver/calibre-web
    container_name: calibre-web
    volumes:
      - /mnt/nfs-media/books:/books
      - /mnt/.docker/calibre-web/config:/config
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/Stockholm
    restart: unless-stopped
    networks:
      - shared-net
#### Cinema ####
  radarr:
    image: lscr.io/linuxserver/radarr:latest
    container_name: radarr
    environment:
      - PUID=0
      - PGID=0
      - TZ=Europe/Stockholm
    volumes:
      - /mnt/.docker/radarr/config:/config
      - type: bind
        source: /mnt/nfs-media/movies
        target: /movies
        bind:
          propagation: rshared
      - type: bind
        source: /mnt/nfs-media/downloads
        target: /downloads
        bind:
          propagation: rshared
    restart: unless-stopped
    networks:
      - shared-net

  jellyfin:
    image: jellyfin/jellyfin:latest
    container_name: jellyfin
    user: "0:0"
    volumes:
      - /mnt/.docker/jellyfin/config:/config
      - type: bind
        source: /mnt/nfs-media/movies
        target: /media/movies
        bind:
          propagation: rshared
    restart: unless-stopped
    devices:
      - /dev/dri:/dev/dri
    deploy:
      resources:
        limits:
          memory: 4g
          cpus: 2.0
        reservations:
          memory: 2g

    networks:
      - shared-net
  bazarr:
    image: lscr.io/linuxserver/bazarr:latest
    container_name: bazarr
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/Stockholm
    volumes:
      - /mnt/.docker/bazarr/config:/config
      - /mnt/nfs-media/movies:/movies
      - /mnt/nfs-media/tv:/tv
    restart: unless-stopped
    networks:
      - shared-net
#### Torrent list ### 
  jackett:
    image: lscr.io/linuxserver/jackett:latest
    container_name: jackett
    environment:
      - PUID=0
      - PGID=0
      - TZ=Europe/Stockholm
    volumes:
      - type: bind
        source: /mnt/nfs-media/jackett/config
        target: /config
        bind:
          propagation: rshared
      - type: bind
        source: /mnt/nfs-media/downloads
        target: /downloads
        bind:
          propagation: rshared
    restart: unless-stopped
    networks:
      - shared-net


networks:
  shared-net:
    external: true
