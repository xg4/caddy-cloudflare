# caddy-cloudflare

This repository builds a custom Caddy image with the `github.com/caddy-dns/cloudflare` plugin and publishes it to GitHub Container Registry as `ghcr.io/<owner>/<repo>`.

The image is built automatically when code is pushed to the `main` branch, and it can also be published manually from the GitHub Actions `workflow_dispatch` trigger.

## Docker Compose

Reference the published image in `docker-compose.yml` and provide a Cloudflare API token through `CLOUDFLARE_API_TOKEN`:

```yaml
services:
  caddy:
    image: ghcr.io/<owner>/<repo>:latest
    ports:
      - "80:80"
      - "443:443"
    environment:
      CLOUDFLARE_API_TOKEN: ${CLOUDFLARE_API_TOKEN}
    volumes:
      - ./Caddyfile:/etc/caddy/Caddyfile:ro
      - caddy_data:/data
      - caddy_config:/config

volumes:
  caddy_data:
  caddy_config:
```

Update `<owner>/<repo>` to match the repository path. The workflow publishes the image using the `ghcr.io/${{ github.repository }}` naming convention.
