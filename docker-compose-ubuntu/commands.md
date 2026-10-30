# Docker + Docker Compose on Ubuntu: commands

From the video: https://youtu.be/gL-rh90PiU8

## 1. Add Docker's signing key

```bash
sudo apt update
sudo apt install -y ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

## 2. Add Docker's software source

Paste the whole block at once.

```bash
sudo tee /etc/apt/sources.list.d/docker.sources << EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Architectures: $(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/docker.asc
EOF
sudo apt update
```

## 3. Install Docker

```bash
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

## 4. Test it

```bash
sudo docker run hello-world
```

## 5. Check the Docker Compose version

```bash
docker compose version
```

## 6. Use Docker without sudo

```bash
sudo usermod -aG docker $USER
```

Log out and back in (or disconnect and reconnect your SSH session) so the change takes effect.

## 7. Confirm it works without sudo

```bash
docker ps
```

## 8. Prepare your first Compose app

```bash
sudo mkdir -p /opt/docker
sudo chown $USER:$USER /opt/docker
mkdir -p /opt/docker/hello-web && cd /opt/docker/hello-web
nano compose.yml
```

## 9. compose.yml

Indentation matters: use two spaces, never tabs.

```yaml
services:
  web:
    image: nginx:alpine
    ports:
      - "8080:80"
```

## 10. Start the app

```bash
docker compose up -d
```

## 11. List the app's running containers

```bash
docker compose ps
```

## 12. Shut the app down

```bash
docker compose down
```

## 13. Update an app (from its folder)

```bash
docker compose pull && docker compose up -d
```

The video runs `docker compose down` first. You can skip it: without it, the app keeps running while the new image downloads, then restarts once.

## 14. Remove the app folder

Shut the app down first (step 12).

```bash
rm -rf /opt/docker/hello-web
```

## 15. Remove stale containers

```bash
docker ps -a                 # find the container ID
docker rm <container-id>     # remove it
```

## 16. Remove stale images

```bash
docker images                # list saved images
docker rmi <image-name>      # remove one
```
