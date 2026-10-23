# Arcane Docker dashboard: commands

From the video: https://youtu.be/1ITJ3Q2jNXI

## 1. Folder and encryption key

```bash
mkdir -p /opt/docker/arcane/data
cd /opt/docker/arcane
echo "ENCRYPTION_KEY=$(openssl rand -base64 32)" > .env
id
```

`id` shows your uid and gid. If they aren't both 1000, change `PUID` and `PGID` in the compose file to match yours.

## 2. Compose file

```bash
nano compose.yaml
```

Paste the contents of [compose.yml](compose.yml). Save with Ctrl+O, Enter, then exit with Ctrl+X.

## 3. Start it

```bash
docker compose up -d
docker ps
```

## 4. Open the Arcane dashboard

Get your Docker server's IP address (run this on that computer):

```bash
hostname -I
```

Copy the first IP address listed and browse to `http://<your-ip>:3552`.

- User name: `arcane`
- Password: `arcane-admin`

You'll have to change the password on your first login.
