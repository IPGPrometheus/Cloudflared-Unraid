# Cloudflared-Unraid

This repository provides multiple ways to run Cloudflare Tunnel (cloudflared) in Docker, including an Unraid template and standard Docker deployment options.

## Overview

Cloudflared is a tunneling daemon that proxies traffic from the Cloudflare network to your origins. This setup runs cloudflared in tunnel mode to securely connect your services to Cloudflare without exposing them to the public internet directly.

## Prerequisites

Before starting, you'll need:
1. A Cloudflare account
2. A domain configured in Cloudflare
3. A Cloudflare tunnel token from the Zero Trust dashboard

### Getting Your Tunnel Token

1. Log in to the [Cloudflare Zero Trust dashboard](https://one.dash.cloudflare.com/)
2. Go to **Access** → **Tunnels**
3. Create a new tunnel or select an existing one
4. Copy the tunnel token

## Installation Methods

### Method 1: Unraid Template

1. Add the template URL to your Unraid Community Applications:
   ```
   https://raw.githubusercontent.com/IPGPrometheus/Cloudflared-Unraid/master/cloudflared.xml
   ```

2. Search for "cloudflared" in Community Applications and install

3. Configure the tunnel token in the template settings

### Method 2: Docker CLI

Run with Docker CLI using your tunnel token:

```bash
docker run -d \
  --name cloudflared \
  --restart unless-stopped \
  -e TUNNEL_TOKEN=your_tunnel_token_here \
  cloudflare/cloudflared:latest \
  tunnel --no-autoupdate run
```

Replace `your_tunnel_token_here` with your actual Cloudflare tunnel token.


### Method 3: Docker Compose

Create a `docker-compose.yml` file:

```yaml
version: '3.8'

services:
  cloudflared:
    image: cloudflare/cloudflared:latest
    container_name: cloudflared
    command: tunnel --no-autoupdate run
    environment:
      - TUNNEL_TOKEN=your_tunnel_token_here
    restart: unless-stopped
    network_mode: bridge
```

Then run:

```bash
# Start the service
docker-compose up -d

# View logs
docker-compose logs -f cloudflared

# Stop the service
docker-compose down
```


## Verification

To verify your tunnel is working:

1. Check container logs:
   ```bash
   # Docker CLI
   docker logs cloudflared
   
   # Docker Compose
   docker-compose logs cloudflared
   ```

2. Look for successful connection messages like:
   ```
   Connection established with Cloudflare edge
   ```

3. Check the tunnel status in your Cloudflare Zero Trust dashboard

## Troubleshooting

### Common Issues

1. **Invalid tunnel token**: Ensure your token is correctly copied from the Cloudflare dashboard
2. **Connection refused**: Check that your tunnel configuration in Cloudflare points to accessible services
3. **Container keeps restarting**: Check logs for specific error messages

### Getting Help

- [Cloudflare Tunnel Documentation](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/)
- [Cloudflared GitHub Repository](https://github.com/cloudflare/cloudflared)
- [Docker Hub Page](https://hub.docker.com/r/cloudflare/cloudflared/)

## Security Notes

- Never commit your tunnel token to version control
- Use environment files or secrets management for production deployments
- Regularly rotate your tunnel tokens
- Monitor tunnel access logs in the Cloudflare dashboard

## Contributing

Feel free to submit issues and enhancement requests!

## License

This project follows the same license as the official Cloudflare cloudflared project.
