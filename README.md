# caddy-cloudflare

This repository builds a custom Caddy image with the `github.com/caddy-dns/cloudflare` plugin and publishes it to GitHub Container Registry as `ghcr.io/<owner>/<repo>`.

The image is built automatically when code is pushed to the `main` branch, and it can also be published manually from the GitHub Actions `workflow_dispatch` trigger.

## Cloudflare API Token

To use the Cloudflare DNS plugin, you need an API token with the following permissions:

1. Go to the [Cloudflare Dashboard - API Tokens](https://dash.cloudflare.com/profile/api-tokens).
2. Click **Create Token**.
3. Use the **Edit zone DNS** template.
4. Ensure the token has these permissions:
   - `Zone - DNS - Edit`
   - `Zone - Zone - Read`
5. Select the specific zone(s) or all zones under **Zone Resources**.

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

## Caddyfile

In your `Caddyfile`, configure the `tls` module to use the `cloudflare` DNS solver with the environment variable provided in the `docker-compose.yml`:

```caddy
example.com {
    tls {
        dns cloudflare {env.CLOUDFLARE_API_TOKEN}
    }
    reverse_proxy localhost:8080
}
```

Or configure it globally for all sites:

```caddy
{
    acme_dns cloudflare {env.CLOUDFLARE_API_TOKEN}
}

example.com {
    reverse_proxy localhost:8080
}
```
