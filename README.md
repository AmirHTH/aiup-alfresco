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

1. **Start an ACS 25.x environment**:

   ```bash
   # Use /7_docker_compose to generate a compose.yaml, then:
   docker compose up -d
   ```

2. **Run the numbered workflow** inside Claude Code:

   ```
   /1_requirements      # Gather requirements as user stories
   /2_content_model     # Generate content model XML
   /3_web_scripts       # Generate Web Script descriptors & controllers
   /4_rest_api          # Generate REST controllers + OpenAPI spec
   /5_behaviours        # Scaffold behaviours/policies
   /6_actions           # Scaffold action executors
   /7_docker_compose    # Generate full ACS stack compose.yaml
   /8_test              # Generate integration tests + Postman collection
   ```

## Commands

| # | Command | Output |
|---|---------|--------|
| 1 | `/1_requirements` | Requirements document (Markdown) |
| 2 | `/2_content_model` | Content model XML + Spring context |
| 3 | `/3_web_scripts` | Web Script descriptor + controller + FreeMarker template |
| 4 | `/4_rest_api` | `@RestController` + DTOs + OpenAPI YAML |
| 5 | `/5_behaviours` | Behaviour/policy class + Spring bean wiring |
| 6 | `/6_actions` | `ActionExecuter` class + bean registration |
| 7 | `/7_docker_compose` | `compose.yaml` with full ACS stack |
| 8 | `/8_test` | Integration test class + Postman collection |

## Skills

Skills are invoked automatically by Claude during command execution:

- **content-model-validator** — validates model XML structure and naming
- **docker-compose-healthcheck-injector** — ensures healthchecks on all services
- **sdk-version-detector** — detects SDK version and adjusts generated code
- **rest-api-convention-enforcer** — checks REST path and paging conventions

## Agents

- **alfresco-architect-agent** — proposes full extension architecture from business requirements
- **alfresco-migrator-agent** — analyses existing AMPs/JARs and produces migration plans
- **alfresco-debugger-agent** — diagnoses stack traces and suggests fixes

## Reference Versions

| Component | Version |
|-----------|---------|
| Alfresco Content Services | 25.x |
| Alfresco SDK | 6.x |
| Java | 17+ |
| Spring Boot | 3.x |
| Docker Compose | v2 |

## Quickstart: Content Model to Docker in 10 Minutes

```
# 1. Scaffold requirements from a business description
/1_requirements "We need to manage technical documents with categories and review dates"

# 2. Generate the content model
/2_content_model

# 3. Generate a REST endpoint for querying documents
/3_web_scripts

# 4. Generate Docker Compose with the full ACS stack
/7_docker_compose

# 5. Generate integration tests
/8_test

# 6. Build, deploy, and verify
mvn clean package
docker compose up -d
mvn verify -Dalfresco.host=http://localhost:8080
```

## Hooks

Three hooks fire automatically during development:

| Hook | Trigger | Action |
|------|---------|--------|
| **pre-commit** | `git commit` with staged `*-model*.xml` or `*-context.xml` | Blocks commit until `content-model-validator` passes |
| **post-generate** | `Edit`/`Write` of artefacts from `/2_*`–`/8_*` | Reminds to update traceability in `REQUIREMENTS.md` |
| **on-error** | Failed `mvn`/`mvnw` command | Invokes `alfresco-debugger-agent` with error output |

## Related Projects

- [aiup-alfresco-reference](https://github.com/aborroy/aiup-alfresco-reference) — reference Platform JAR for end-to-end validation

## License

[Apache License 2.0](LICENSE)
