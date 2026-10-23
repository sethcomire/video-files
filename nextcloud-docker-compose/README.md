# Self-Host Nextcloud in 15 Minutes: Docker Compose Tutorial

Video: https://youtu.be/InGS69ODP3E (2026-02-16)

- [compose.yml](compose.yml): the compose file
- [.env.example](.env.example): the passwords file

## Quick start

1. Make a folder for Nextcloud and put both files in it.
2. Copy the example passwords file and set real passwords:

   ```bash
   cp .env.example .env
   nano .env
   ```

3. In `compose.yml`, change every `/your/desired/folder/` to the folder you want to use.
4. Start it:

   ```bash
   docker compose up -d
   ```

5. Browse to `http://localhost:8080` (or your server's IP address, port 8080).

Never share or commit your real `.env` file.
