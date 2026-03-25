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
- `compose.yaml` always lives at the repository root, even in Mixed mode
- If a Platform JAR project is present, mount or copy its built artifact from the Platform JAR `Root path`
- If an Out-of-Process Spring Boot app is present, add it as a service that depends on `activemq` and `alfresco` with `condition: service_healthy`
- If an Out-of-Process Spring Boot app is present, its `build:` context must point to the Event Handler `Root path` from `REQUIREMENTS.md` (`.` for Event Handler only mode, `{name}-events/` for Mixed mode)
- Never assume the Platform JAR and Event Handler share the same build context or the same deployable artifact

## Canonical Image Tags (ACS 26.1) — use exactly as written, never infer or abbreviate

> **RULE**: Copy these tags verbatim. Do **not** shorten (e.g. `26.1` instead of `26.1.0`),
> do **not** invent patch versions (e.g. `5.1.2` instead of `5.4.0`), and do **not** drop
> OS/JRE suffixes (e.g. `6.2.1` instead of `6.2.1-jre17-rockylinux8`).
> If a tag is absent from this table, look it up in AGENTS.md §Docker Images — never guess.

### Profile B — Search Services / Solr (community default)

```
alfresco/alfresco-content-repository-community:26.1.0
alfresco/alfresco-share:26.1.0
alfresco/alfresco-search-services:2.0.18
postgres:17.9
docker.io/alfresco/alfresco-activemq:6.2.1-jre17-rockylinux8
alfresco/alfresco-transform-core-aio:5.4.0
nginx:stable-alpine
```

### Profile A — Search Enterprise / OpenSearch (recommended for production)

```
alfresco/alfresco-content-repository-community:26.1.0
alfresco/alfresco-share:26.1.0
opensearchproject/opensearch:2.x
postgres:17.9
docker.io/alfresco/alfresco-activemq:6.2.1-jre17-rockylinux8
alfresco/alfresco-transform-core-aio:5.4.0
nginx:stable-alpine
```

## Share Healthcheck — critical note

`/share/page/home` does **not** exist in Share 26.x and causes a Spring Surf 500 error,
which makes the `share` service stay in `health: starting` forever and blocks `proxy` from starting.

Use this healthcheck instead — it accepts any 2xx or 3xx response from the Share root:

```yaml
healthcheck:
  test: ["CMD-SHELL", "curl -s -o /dev/null -w '%{http_code}' http://localhost:8080/share/ | grep -qE '^[23]'"]
  interval: 30s
  timeout: 10s
  retries: 5
  start_period: 30s
```

## Encryption Keystore (mandatory for ACS 26.1)

ACS 26.1 ships with a JCEKS keystore inside the image at
`/usr/local/tomcat/shared/classes/alfresco/extension/keystore/keystore`.
Use it directly — **no custom keystore generation or host volume mount is needed**.

Only `JAVA_TOOL_OPTIONS` is required, pointing at the bundled path and supplying the correct passwords:

```yaml
alfresco:
  environment:
    JAVA_TOOL_OPTIONS: >-
      -Dencryption.keystore.type=JCEKS
      -Dencryption.cipherAlgorithm=DESede/CBC/PKCS5Padding
      -Dencryption.keyAlgorithm=DESede
      -Dencryption.keystore.location=/usr/local/tomcat/shared/classes/alfresco/extension/keystore/keystore
      -Dmetadata-keystore.password=mp6yc0UD9e
      -Dmetadata-keystore.aliases=metadata
      -Dmetadata-keystore.metadata.password=oKIWzVdEdA
      -Dmetadata-keystore.metadata.algorithm=DESede
```

> **Common pitfall**: using `oKIWzOIvdD` (wrong) instead of `oKIWzVdEdA` (correct) for
> `-Dmetadata-keystore.metadata.password` causes `02240004 Failed to retrieve keys from keystore:
> Given final block not properly padded`. Always use `oKIWzVdEdA`.
> Omitting `JAVA_TOOL_OPTIONS` entirely fails with `02240000 Unable to get secret key: no key information is provided`.

## Validation
After generation, invoke `docker-compose-healthcheck-injector` skill.
