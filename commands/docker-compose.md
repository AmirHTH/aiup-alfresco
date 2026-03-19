---
description: "Generate a Docker Compose file with full ACS stack."
allowed-tools: "Read, Write, Grep, Glob"
argument-hint: "[ACS version, e.g. 25.3]"
---

# /docker-compose — Docker Compose Generator

Generate a complete `compose.yaml` for an Alfresco Content Services stack.

## Input
- ACS version from "$ARGUMENTS" or default to 25.3
- Read `REQUIREMENTS.md` for any deployment-specific requirements

## Output File
`compose.yaml` at project root.

## Required Services

### Core
- `alfresco` — ACS repository (with extension JAR mounted or built into image)
- `postgres` — PostgreSQL database
- `activemq` — Apache ActiveMQ message broker

### Transform
- `transform-core-aio` — All-in-one Transform Service

### Search (choose one based on requirements)
- `elasticsearch` — for Search Enterprise
- `solr6` — for Solr-based search

### Optional
- `share` — Share UI (if content model forms are needed)
- `proxy` — Nginx or similar reverse proxy
- `content-app` — Alfresco Content App (ACA)

## Conventions
- Use Docker Compose v2 format (no `version:` key)
- Every service must have a `healthcheck`
- Use `condition: service_healthy` in `depends_on`
- Use named volumes for persistent data
- Reference CLAUDE.md for image tags and environment variables

## Validation
After generation, invoke `docker-compose-healthcheck-injector` skill.
