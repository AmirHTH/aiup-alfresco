# aiup-alfresco

A [Claude Code](https://claude.com/claude-code) plugin that packages Alfresco/Hyland extension development as slash commands, skills, and agents — lowering the barrier for new developers and accelerating experienced ones.

## Prerequisites

- [Claude Code](https://claude.com/claude-code) CLI installed and configured
- Java 17+
- Maven 3.9+
- Docker & Docker Compose

## Installation

```bash
claude plugin install aborroy/aiup-alfresco
```

Or for local development:

```bash
claude --plugin-dir /path/to/aiup-alfresco
```

## Quick Start

1. **Start an ACS 26.1 environment**:

   ```bash
   # Use /docker-compose to generate a compose.yaml, then:
   docker compose up -d
   ```

2. **Run the workflow** inside Claude Code:

   **In-Process extensions** (behaviours, web scripts, actions — deployed as JAR/AMP inside ACS):
   ```
   /requirements        # Gather requirements as user stories
   /content-model       # Generate content model XML
   /web-scripts         # Generate Web Script descriptors & controllers
   /behaviours          # Scaffold behaviours/policies
   /actions             # Scaffold action executors
   /docker-compose      # Generate full ACS stack compose.yaml
   /test                # Generate integration tests
   ```

   **Out-of-Process extensions** (event listeners — deployed as a separate Spring Boot app):
   ```
   /requirements        # Gather requirements as user stories
   /events              # Generate Spring Boot event listener
   /docker-compose      # Generate full ACS stack + listener service compose.yaml
   /test                # Generate integration tests
   ```

## Commands

| Command | SDK | Output |
|---------|-----|--------|
| `/requirements` | Both | Requirements document (Markdown) |
| `/content-model` | In-Process | Content model XML + Spring context |
| `/web-scripts` | In-Process | Web Script descriptor + controller + FreeMarker template |
| `/behaviours` | In-Process | Behaviour/policy class + Spring bean wiring |
| `/actions` | In-Process | `ActionExecuter` class + bean registration |
| `/events` | Out-of-Process | Spring Boot event listener + ActiveMQ config |
| `/docker-compose` | Both | `compose.yaml` with full ACS stack |
| `/test` | Both | Integration test class |

## Skills

Skills are invoked automatically by Claude during command execution:

- **content-model-validator** — validates model XML structure and naming *(In-Process)*
- **docker-compose-healthcheck-injector** — ensures healthchecks on all services *(Both)*
- **sdk-version-detector** — detects In-Process vs Out-of-Process SDK and adjusts generated code *(Both)*
- **event-api-topology-checker** — validates ActiveMQ topic names and event consumer patterns *(Out-of-Process)*

## Agents

- **alfresco-architect-agent** — proposes full extension architecture from business requirements
- **alfresco-migrator-agent** — analyses existing AMPs/JARs and produces migration plans
- **alfresco-debugger-agent** — diagnoses stack traces and suggests fixes

## Reference Versions

| Component | Version |
|-----------|---------|
| Alfresco Content Services | 26.1 |
| Maven In-Process SDK | 4.15.0 |
| Spring Boot Out-of-Process SDK | 7.2.0 |
| Java | 17+ |
| Spring Boot | 3.x |
| Docker Compose | v2 |

## Quickstart: In-Process Extension (Content Model + Web Scripts)

```
# 1. Scaffold requirements from a business description
/requirements "We need to manage technical documents with categories and review dates"

# 2. Generate the content model
/content-model

# 3. Generate Web Scripts for querying documents
/web-scripts

# 4. Generate Docker Compose with the full ACS stack
/docker-compose

# 5. Generate integration tests
/test

# 6. Build, deploy, and verify
mvn clean package
docker compose up -d
mvn verify -Dalfresco.host=http://localhost:8080
```

## Quickstart: Out-of-Process Extension (Event Listener)

```
# 1. Scaffold requirements
/requirements "Notify an external system when a document is approved"

# 2. Generate the Spring Boot event listener
/events

# 3. Generate Docker Compose (ACS stack + listener service)
/docker-compose

# 4. Generate integration tests
/test

# 5. Build and deploy
mvn clean package
docker compose up -d
```

## Hooks

Three hooks fire automatically during development:

| Hook | Trigger | Action |
|------|---------|--------|
| **pre-commit** | `git commit` with staged `*-model*.xml` or `*-context.xml` | Blocks commit until `content-model-validator` passes |
| **post-generate** | `Edit`/`Write` of artefacts from `/content-model` through `/test` | Reminds to update traceability in `REQUIREMENTS.md` |
| **on-error** | Failed `mvn`/`mvnw` command | Invokes `alfresco-debugger-agent` with error output |

## Related Projects

- [aiup-alfresco-reference](https://github.com/aborroy/aiup-alfresco-reference) — reference Platform JAR for end-to-end validation

## License

[Apache License 2.0](LICENSE)
