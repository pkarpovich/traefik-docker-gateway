# Traefik Docker Gateway

This repository contains a Docker Compose setup for running a Traefik gateway. Traefik serves as a reverse proxy and
load balancer, simplifying the deployment and routing of services with automatic SSL/TLS encryption via Let's Encrypt.

## Configuration

Before starting, you need to set up your environment variables. Create a .env file in the root of this project with the
following variables:

- `EMAIL`: Your email for Let's Encrypt notifications.
- `DASHBOARD_DOMAIN`: The domain for accessing the Traefik dashboard.
- `DASHBOARD_AUTH`: Basic Auth for securing the Traefik dashboard. Use htpasswd to generate credentials.

Example `.env` file:

```dotenv
EMAIL=your-email@example.com
DASHBOARD_DOMAIN=traefik.example.com
DASHBOARD_AUTH=username:$$apr1$$hjKLlZ2E$$Y8rGk7yZt2hEtyyRDT/s5/
```

Note that `DASHBOARD_AUTH` should be escaped with double dollar signs.

## Networking

Traefik is configured to use an external network named proxy. Ensure this network is created before starting the
services:

```bash
docker network create proxy
```

## Usage

To add a service to Traefik, add the following labels to the service's Docker Compose configuration:

```yaml
labels:
    - "traefik.enable=true"
    - "traefik.http.routers.<service-name>.rule=Host(`<service-domain>`)"
    - "traefik.http.routers.<service-name>.entrypoints=web-secure"
```

Also container should be added to the proxy network

Full example:

```yaml
services:
    esport-syncer:
        build: .
        container_name: esport-syncer
        restart: unless-stopped
        expose:
            - 1984
        networks:
            - proxy
        labels:
            - "traefik.enable=true"
            - "traefik.http.routers.esport-syncer.rule=Host(`${DOMAIN}`)"
            - "traefik.http.routers.esport-syncer.entrypoints=web-secure"
```

## License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.