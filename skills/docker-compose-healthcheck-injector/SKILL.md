---
description: "Ensures every service in a Docker Compose file has a healthcheck block and depends_on uses condition: service_healthy. Trigger when generating or editing compose.yaml or docker-compose.yml."
user-invocable: false
allowed-tools: "Read, Grep, Glob"
---

# Docker Compose Healthcheck Injector

Validate and fix Docker Compose files for Alfresco deployments.

## Required Services
Verify these services are present (when applicable):
- `alfresco` — ACS repository
- `postgres` — database
- `activemq` — message broker
- `transform-core-aio` or individual transform services
- `search` — Solr or Elasticsearch
- `share` — (optional, if Share UI is used)

## Healthcheck Rules
Every service must have a `healthcheck` block with:
- `test` — appropriate health endpoint or command
- `interval` — recommended 30s
- `timeout` — recommended 10s
- `retries` — recommended 3
- `start_period` — recommended 60s for Alfresco, 30s for others

## Dependency Rules
- `depends_on` must use `condition: service_healthy` (not just service name)
- `alfresco` depends on `postgres` and `activemq`
- `share` depends on `alfresco`
- `search` depends on `alfresco` (for Solr) or is independent (Elasticsearch)

## Known Healthcheck Endpoints
- Alfresco: `curl -f http://localhost:8080/alfresco/api/-default-/public/alfresco/versions/1/probes/-ready-`
- Share: `curl -f http://localhost:8080/share/page`
- ActiveMQ: `curl -f http://localhost:8161/admin`
- PostgreSQL: `pg_isready -U alfresco`
- Elasticsearch: `curl -f http://localhost:9200/_cluster/health`
- Transform: `curl -f http://localhost:8090/ready`

## Output
List all missing healthchecks and incorrect dependencies. Provide corrected YAML blocks.
