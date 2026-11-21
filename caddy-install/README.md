# Caddy: Real HTTPS for Your Self-Hosted Apps

Video: coming soon
Written guide, with every command explained: https://sethcomire.com/caddy-install/

Sets up Caddy, a reverse proxy, with real Let's Encrypt certificates for your apps. It proves you own the domain through Cloudflare DNS, so nothing on your router gets opened. The domain points at your server's Tailscale address. Needs [Docker + Docker Compose](../docker-compose-ubuntu/) first, Tailscale on the server and your devices, and a domain on Cloudflare.

- [Dockerfile](Dockerfile): builds Caddy with the Cloudflare DNS plugin
- [compose.yml](compose.yml): the compose file, for `/opt/docker/caddy`
- [conf/Caddyfile](conf/Caddyfile): Caddy's settings, which name goes to which app. It lives in the `conf` folder, which is mounted at `/etc/caddy`, because Caddy's Docker docs advise against mounting the Caddyfile as a single file (some editors replace the file, and Caddy would keep reading the old one)
- [.env.example](.env.example): what the `.env` file looks like (put your own Cloudflare API token in)

In the Caddyfile, change `arcane.sethshomelab.com` to your own name, and `3552` to your app's port.

## Before you start Caddy: your app names go public

The moment Caddy gets a certificate, that name goes into public Certificate Transparency logs, for good. Anyone can search them, for example with [SSLMate's certificate search](https://sslmate.com/ct_search_api/). With one certificate per app, that's a list of every app you run. Nobody can reach the apps without Tailscale, but if you'd rather keep the names private, use one wildcard certificate instead, **before** you start Caddy for the first time:

```
*.sethshomelab.com {
	tls {
		dns cloudflare {env.CF_API_TOKEN}
	}
	abort
}

arcane.sethshomelab.com {
	reverse_proxy host.docker.internal:3552
}
```

- The wildcard block gets one certificate that covers every name under your domain, so the logs only show `*.sethshomelab.com`.
- `abort` means any name without its own block gets nothing.
- Each app block only needs its `reverse_proxy` line; Caddy uses the wildcard certificate for it (Caddy 2.10 and newer).

Names already in the logs stay there, so switching later only hides new ones. You may also see a wildcard certificate from Cloudflare in the results; Cloudflare makes it for your domain on its own, and it doesn't list your apps.

## Updating Caddy

Caddy is built on your server from the Dockerfile, so it doesn't update on its own, and tools that update containers automatically can't update it. To update, rebuild it from the Caddy folder:

```bash
cd /opt/docker/caddy
docker compose build --pull
docker compose up -d
```

- `build --pull` checks for newer Caddy images first, then rebuilds Caddy with the newest Cloudflare plugin.
- `up -d` replaces the running Caddy with the new one. Your certificates are in the `data` folder, so they carry over.

**Which version you get** depends on the two `FROM` lines in the Dockerfile:

- **`2.11`** (`caddy:2.11-builder` and `caddy:2.11`, what the Dockerfile uses): every rebuild gets the latest bug and security fixes for 2.11, but never 2.12. When a new minor version comes out, read the [release notes](https://github.com/caddyserver/caddy/releases), change both lines, then rebuild.
- **A full version number** (like `caddy:2.11.7-builder` and `caddy:2.11.7`): you stay on exactly that version until you change the number.
- **Just `2`** (`caddy:2-builder` and `caddy:2`): every rebuild gets the newest Caddy 2, including new minor versions. It never jumps to a future Caddy 3 that might change how the Caddyfile works, which `latest` could.

Keep both `FROM` lines on the same version.

### Moving to a newer version

By the time you read this, Caddy is probably past 2.11. Check the newest version on the [releases page](https://github.com/caddyserver/caddy/releases) and read what changed. Then open the Dockerfile:

```bash
cd /opt/docker/caddy
nano Dockerfile
```

Change the number in both `FROM` lines, for example from `2.11` to `2.12`:

```dockerfile
FROM caddy:2.12-builder AS builder
...
FROM caddy:2.12
```

Save, then rebuild with the two commands above. Docker Hub can take a day or two to publish a new release's images, so if the build says the image isn't found, wait and try again.

To apply a Caddyfile change without restarting Caddy:

```bash
docker exec -w /etc/caddy caddy caddy reload
```

Official docs: https://caddyserver.com/docs/ and the Caddy Docker image, with the builder: https://hub.docker.com/_/caddy
