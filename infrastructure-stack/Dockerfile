version: '2.2'

services:
  nginxproxymanager:
    image: 'jc21/nginx-proxy-manager:latest'
    container_name: nginxproxymanager
    restart: unless-stopped
    ports:
      - '80:80'
      - '81:81'
      - '443:443'
    volumes:
      - /mnt/.docker/nginx/data:/data
      - /mnt/.docker/nginx/letsencrypt:/etc/letsencrypt
    networks:
      - shared-net

  nextcloud:
    image: lscr.io/linuxserver/nextcloud:latest
    container_name: nextcloud
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/Berlin
    volumes:
      - /mnt/.docker/nginx/nextcloud/appdata:/config
      - /mnt/.docker/nginx/nextcloud/data:/data
    networks:
      - shared-net
    restart: unless-stopped

  homeassistant:
    image: lscr.io/linuxserver/homeassistant:latest
    container_name: homeassistant
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/Berlin
    volumes:
      - /mnt/.docker/nginx/hass/config:/config
    networks:
      - shared-net
    restart: unless-stopped

  oauth2-proxy:
    image: quay.io/oauth2-proxy/oauth2-proxy:v7.6.0
    container_name: oauth2-proxy
    depends_on:
      - nginxproxymanager
    environment:
      - OAUTH2_PROXY_PROVIDER=github
      - OAUTH2_PROXY_CLIENT_ID=a*********9 # replace it with yours 
      - OAUTH2_PROXY_CLIENT_SECRET=a***********7 # replace it with yours 
      - OAUTH2_PROXY_COOKIE_SECRET=a**************A== # replace it with yours 
      - OAUTH2_PROXY_COOKIE_SECURE=true
      - OAUTH2_PROXY_COOKIE_SAMESITE=lax
      - OAUTH2_PROXY_COOKIE_DOMAINS=.example.com # replace it with yours 
      - OAUTH2_PROXY_SET_XAUTHREQUEST=true
      - OAUTH2_PROXY_PASS_ACCESS_TOKEN=true
      - OAUTH2_PROXY_PASS_USER_HEADERS=true
      - OAUTH2_PROXY_SCOPE=read:org user:email
      - OAUTH2_PROXY_EMAIL_DOMAINS=*
      - OAUTH2_PROXY_REDIRECT_URL=https://auth.example.com/oauth2/callback # replace it with yours 
      - OAUTH2_PROXY_HTTP_ADDRESS=0.0.0.0:4180
      - OAUTH2_PROXY_SKIP_PROVIDER_BUTTON=true
      - OAUTH2_PROXY_REVERSE_PROXY=true
      - OAUTH2_PROXY_GITHUB_ORG=G******z # replace it with yours 
      - OAUTH2_PROXY_SHOW_DEBUG_ON_ERROR=true
      - OAUTH2_PROXY_LOG_LEVEL=debug
      - OAUTH2_PROXY_WHITELIST_DOMAINS=.example.com # replace it with yours 
    ports:
      - "4180:4180"
    networks:
      - shared-net

  internal-code-server:
    image: lscr.io/linuxserver/code-server:latest
    container_name: internal-code-server
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
      - HOME=/config
      - LANGUAGE=en_US.UTF-8
      - LANG=en_US.UTF-8
      - TERM=xterm
      - S6_CMD_WAIT_FOR_SERVICES_MAXTIME=0
      - S6_VERBOSITY=1
      - S6_STAGE2_HOOK=/docker-mods
      - VIRTUAL_ENV=/lsiopy
      - LSIO_FIRST_PARTY=true
    volumes:
      - /mnt/.docker/code-server/config:/config
      - /mnt/.docker/homepage/config:/homepage
      - /mnt/.docker/nginx-stack:/proxy
    restart: "no"
    cap_add:
      - AUDIT_WRITE
      - CHOWN
      - DAC_OVERRIDE
      - FOWNER
      - FSETID
      - KILL
      - MKNOD
      - NET_BIND_SERVICE
      - NET_RAW
      - SETFCAP
      - SETGID
      - SETPCAP
      - SETUID
      - SYS_CHROOT
    cap_drop:
      - AUDIT_CONTROL
      - BLOCK_SUSPEND
      - DAC_READ_SEARCH
      - IPC_LOCK
      - IPC_OWNER
      - LEASE
      - LINUX_IMMUTABLE
      - MAC_ADMIN
      - MAC_OVERRIDE
      - NET_ADMIN
      - NET_BROADCAST
      - SYSLOG
      - SYS_ADMIN
      - SYS_BOOT
      - SYS_MODULE
      - SYS_NICE
      - SYS_PACCT
      - SYS_PTRACE
      - SYS_RAWIO
      - SYS_RESOURCE
      - SYS_TIME
      - SYS_TTY_CONFIG
      - WAKE_ALARM
    networks:
      - shared-net
    labels:
      org.opencontainers.image.title: "Code-server"
      org.opencontainers.image.description: "VS Code running in the browser"
      org.opencontainers.image.documentation: "https://docs.linuxserver.io/images/docker-code-server"
      org.opencontainers.image.source: "https://github.com/linuxserver/docker-code-server"
      org.opencontainers.image.version: "4.100.1-ls272"
  wip:
    container_name: wip
    image: eigenfocus/eigenfocus:1.2.0-free
    restart: unless-stopped
    volumes:
      - /mnt/.docker/eigenfocus/app-data:/eigenfocus-app/app-data
    environment:
     - DEFAULT_HOST_URL=https://wip.example.com
#    ports:
#      - 3001:3000
    networks:
      - shared-net
  lab-dash:
      container_name: lab-dash
      image: ghcr.io/anthonygress/lab-dash:latest
      privileged: true
      #network_mode: host # for monitoring network usage stats
      environment:
        - SECRET=I*****************U= # any random string for used for encryption.
        # You can run `openssl rand -base64 32` to generate a key
      volumes:
        - /sys:/sys:ro
        - /mnt/.docker/lab-dash/config:/config
        - /mnt/.docker/lab-dash/uploads:/app/public/uploads
        - /var/run/docker.sock:/var/run/docker.sock
      restart: unless-stopped
      labels:
        - "com.centurylinklabs.watchtower.enable=true"
      networks:
        - shared-net
  stirling-pdf:
    image: frooodle/s-pdf:latest
    container_name: stirling-pdf
#    ports:
#      - "8080:8080"  # Access at http://localhost:8080
    volumes:
      - /mnt/.docker/pdf/stirling-data:/usr/share/tessdata  # For OCR languages
      - /mnt/.docker/pdf/configs:/configs  # Optional config storage
    environment:
      - LANGS=en+de+fr+ar+sv  # Add OCR languages you need
      - DOCKER_ENABLE_SECURITY=false
    restart: unless-stopped
    networks:
      - shared-net

networks:
  shared-net:
    external: true
