# GTLabs.IaC

Infrastructure-as-code for GT Labs microservice infrastructure.

This compose stack runs the shared infrastructure used by GT Labs microservices.

## Services

- PostgreSQL
- Redis
- RabbitMQ with management UI
- Consul
- Fabio gateway
- Seq
- OpenTelemetry Collector
- Jaeger

## First Run

Create a private environment file from the template:

```bash
cp .env.infrastructure.example .env.infrastructure
```

Edit `.env.infrastructure` and replace all `replace-with-*` values with real secrets.

Start the stack:

```bash
docker compose --env-file .env.infrastructure -f docker-compose.infrastructure.yml up -d
```

Stop the stack:

```bash
docker compose --env-file .env.infrastructure -f docker-compose.infrastructure.yml down
```

## URLs

- Consul: `http://localhost:8500`
- Fabio gateway: `http://localhost:9999`
- Fabio UI: `http://localhost:9998`
- RabbitMQ UI: `http://localhost:15672`
- Seq: `http://localhost:5341`
- Jaeger: `http://localhost:16686`

## Consul K/V Bootstrap

The stack writes shared service configuration to:

```text
General
```

This includes only app-neutral configuration: Redis, RabbitMQ, JWT validation, Seq, OpenTelemetry, and gateway URL configuration.

Per-service configuration must be written separately under each service `AppId`, for example `<AppId>/base`. This infrastructure stack does not create service-specific K/V entries.

## Microservice Environment

Services running inside the same Docker network should use:

```bash
URL-CONSUL=http://consul:8500
SERVICE_ADVERTISE_HOST=<service-container-name-or-host>
SERVICE_ADVERTISE_PORT=<service-port>
```

Services running directly on the homelab host should use:

```bash
URL-CONSUL=http://localhost:8500
SERVICE_ADVERTISE_HOST=<homelab-host-ip-or-dns>
SERVICE_ADVERTISE_PORT=<service-port>
```

If services run outside Docker, set `GATEWAY_URL` in `.env.infrastructure` to `http://<homelab-host-ip-or-dns>:9999` before starting the stack.
