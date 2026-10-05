## Commands

Folder and key

bash

```bash
mkdir -p /opt/docker/arcane/data
cd /opt/docker/arcane
echo "ENCRYPTION_KEY=$(openssl rand -base64 32)" > .env
id  ## Check to see if uid and gid are both 1000. If they're not, then change the compose file PUID and GUID to match yours
```

Compose file

bash

```bash
nano compose.yaml
```

yaml

```yaml
services:
  arcane:
    image: ghcr.io/getarcaneapp/manager:latest
    container_name: arcane
    restart: unless-stopped
    cgroup: host
    ports:
      - 3552:3552
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - /opt/docker/arcane/data:/app/data
      - /opt/docker:/opt/docker
    environment:
      - PUID=1000
      - PGID=1000
      - ENCRYPTION_KEY=${ENCRYPTION_KEY}
      - PROJECTS_DIRECTORY=/opt/docker
```

Save with Ctrl+O, Enter, then exit with Ctrl+X.

Start it

bash

```bash
docker compose up -d
docker ps
```

Browse to Arcane Dashboard
First you need to get your Docker server's IP address. From a terminal session on that computer:

bash

```bash
hostname -I
```
Copy the first IP address listed. Browse to http://(paste your ip address):3552
  
User name: arcane
Password: arcane-admin

You'll have to change the password on your first login.