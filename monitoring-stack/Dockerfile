version: '3.8'

services:
  gotify:
    image: gotify/server
    container_name: gotify
#    ports:
#      - "8080:80"
    volumes:
      - /mnt/.docker/gotify-data:/app/data
    networks:
      - shared-net
    restart: unless-stopped
  igotify:
      container_name: igotify
      hostname: igotify
      image: ghcr.io/androidseb25/igotify-notification-assist:latest
      restart: unless-stopped
      security_opt:
        - no-new-privileges:true
      pull_policy: always
      networks:
        - shared-net
#      ports:
#        - "8681:8080"
      volumes:
        - /mnt/.docker/gotify-data/api-data:/app/data
      environment:                 # option environment see above note
        GOTIFY_URLS:          'http://gotify'
        GOTIFY_CLIENT_TOKENS: 'C_***********G'# Replace with yours 
        SECNTFY_TOKENS:       'NTFY-DEVICE-K----------z' # Replace with yours 
  uptime-kuma:
    image: louislam/uptime-kuma:latest
    container_name: uptime-kuma
    volumes:
      - /mnt/.docker/uptime-kuma:/app/data
#    ports:
#      - "3001:3001"
    networks:
      - shared-net
    restart: unless-stopped

  alertmanager:
    image: prom/alertmanager
    container_name: alertmanager
    volumes:
      - /mnt/.docker/alertmanager:/etc/alertmanager
    command:
      - --config.file=/etc/alertmanager/alertmanager.yml
#    ports:
#      - "9093:9093"
    networks:
      - shared-net
    restart: unless-stopped

networks:
  shared-net:
    external: true
