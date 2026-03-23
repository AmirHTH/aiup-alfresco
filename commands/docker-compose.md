---
description: "Generate a Docker Compose file with full ACS stack."
allowed-tools: "Read, Write, Grep, Glob"
argument-hint: "[ACS version, e.g. 26.1]"
---

# /docker-compose — Docker Compose Generator

Generate a complete `compose.yaml` for an Alfresco Content Services stack.

## Input
- ACS version from "$ARGUMENTS" or default to 26.1
- Read `REQUIREMENTS.md` for any deployment-specific requirements
- Resolve the `Root path` values from Section 2 (Project Architecture)

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
- `{extension-name}` — Out-of-Process Spring Boot app (if `/events` command was used)

## Conventions
- Use Docker Compose v2 format (no `version:` key)
- Every service must have a `healthcheck`
- Use `condition: service_healthy` in `depends_on`
- Use named volumes for persistent data
- Reference CLAUDE.md for image tags and environment variables
- `compose.yaml` always lives at the repository root, even in Mixed mode
- If a Platform JAR project is present, mount or copy its built artifact from the Platform JAR `Root path`
- If an Out-of-Process Spring Boot app is present, add it as a service that depends on `activemq` and `alfresco` with `condition: service_healthy`
- If an Out-of-Process Spring Boot app is present, its `build:` context must point to the Event Handler `Root path` from `REQUIREMENTS.md` (`.` for Event Handler only mode, `{name}-events/` for Mixed mode)
- Never assume the Platform JAR and Event Handler share the same build context or the same deployable artifact

## Validation
After generation, invoke `docker-compose-healthcheck-injector` skill.
