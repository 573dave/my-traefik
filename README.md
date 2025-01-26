# Traefik 3.3 + Portainer Stack with OAuth

A secure, production-ready Docker stack featuring Traefik 3.3 as reverse proxy with Google OAuth authentication and Portainer for container management.

## Features

- Traefik 3.3 reverse proxy with Dashboard
- Automatic HTTPS with Cloudflare DNS challenge
- Google OAuth authentication for all services
- Portainer CE for container management
- Secure headers and TLS configuration
- Rate limiting
- WebSocket support
- Access logs and metrics

## Prerequisites

- Docker and Docker Compose
- Domain name with Cloudflare DNS
- Google OAuth credentials
- Linux server (tested on Ubuntu 22.04)

## Setup Instructions

1. Clone the repository:
```bash
git clone https://github.com/573dave/my-traefik .
```

2. Create required directories:
```bash
chmod 600 traefik/acme.json
```

3. Create and configure secrets:
```bash
# Add Cloudflare DNS API token
echo "your-cloudflare-token" > secrets/cf_dns_api_token.txt

# Generate and add basic auth credentials
docker run --rm httpd:2.4-alpine htpasswd -nb admin your_password_here > secrets/basic_auth_credentials.txt

# Configure OAuth
cat << EOF > secrets/traefik_forward_auth.txt
providers.google.client-id=your-client-id
providers.google.client-secret=your-client-secret
secret=random-secret-string
whitelist=your@email.com
EOF
```

4. Copy and configure environment variables:
```bash
cp .env.example .env
```
Edit `.env` and set your domain and other configurations.

5. Deploy the stack:
```bash
docker-compose up -d
```

6. Convert Portainer login to OAuth:
[Gist](https://gist.github.com/573dave/4d37824fcea9e3d697bae2cd922769ea)

## Usage

### Accessing Services

- Traefik Dashboard: https://traefik.yourdomain.com
- Portainer: https://portainer.yourdomain.com

### Adding New Services

In Portainer, create new containers easily with just one label:
```yaml
traefik.http.services.YOUR_APP.loadbalancer.server.port=YOUR_PORT
```

The service will be automatically available at `yourapp.yourdomain.com` with:
- HTTPS
- OAuth authentication
- Security headers
- Rate limiting
- WebSocket support

## Security Notes

- All services are protected by Google OAuth
- TLS 1.2+ with secure cipher suites
- Rate limiting prevents brute force attempts
- Security headers protect against common web vulnerabilities
- Regular security updates via watchtower (optional)

## Contributing

Pull requests are welcome. For major changes, please open an issue first.

## License

[MIT](https://choosealicense.com/licenses/mit/)
