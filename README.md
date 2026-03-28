# MAW2 Causeries Compose Setup

This directory contains the Docker Compose setup for the MAW2 Causeries backend stack.

## Services

- `gateway`: Nginx reverse proxy exposed on `GATEWAY_PORT` (default `8080` from `.env.example`)
- `api`: Rails API container
- `db`: MySQL 8.0 database for the API
- `apichat`: chat API service
- `scylla`: ScyllaDB instance used by the chat service

## Prerequisites

- Docker
- Docker Compose support (`docker compose`)

## Configuration

1. Copy the example environment file:

```bash
cp .env.example .env
```

2. Fill in the required secrets in `.env`:

- `JWT_SECRET`
- `RAILS_MASTER_KEY`
- `DATABASE_PASSWORD`
- `DATABASE_ROOT_PASSWORD`
- `GATEWAY_PORT`

Example secret generation:

```bash
openssl rand -hex 32
openssl rand -hex 24
```

## Start The Stack

```bash
docker compose -f compose.yml up -d
```

To stop it:

```bash
docker compose -f compose.yml down
```

To view logs:

```bash
docker compose -f compose.yml logs -f
```

## Data Persistence

Persistent data is stored in `./data`:

- `data/api`: Rails storage
- `data/db`: MySQL data
- `data/scylla`: ScyllaDB data

## Exposed Routes

The Nginx gateway forwards requests to the internal services:

- `/api/v1` -> Rails API
- `/api/v1/channels/:channel_id/messages` -> chat API
- `/api/v1/ws` -> chat WebSocket endpoint

## Notes

- The services use a custom Docker bridge network with fixed internal IP addresses.
- `api` starts after MySQL is healthy.
- `apichat` starts after ScyllaDB is healthy.
