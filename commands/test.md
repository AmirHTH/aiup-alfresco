---
description: "Generate integration test classes and HTTP API test scripts for Alfresco extensions."
allowed-tools: "Read, Write, Grep, Glob"
argument-hint: "[path to REQUIREMENTS.md or description]"
---

# /test — Test Generator

Generate tests for the Alfresco extension.

## Input
Read `REQUIREMENTS.md` and all generated artefacts from previous commands.

## Output Files

### In-Process SDK (Maven) — Platform JAR / AMP

#### 1. Integration Test
`src/test/java/{package}/{Name}IT.java`
- JUnit 5, Alfresco SDK integration test runner (runs against a live ACS container)
- Test content model deployment (type/aspect creation)
- Test Web Script endpoints (if generated)
- Test behaviours and actions (if generated)

#### 2. HTTP API Test Scripts
`http-tests/{extension-name}.sh`
- Plain shell scripts using `curl`
- Cover: happy path, validation errors (400), not found (404), unauthorized (401)
- Use environment variables for `HOST`, `USERNAME`, `PASSWORD`

### Out-of-Process SDK (Spring Boot) — Event Listeners

#### 1. Unit Tests
`src/test/java/{package}/handler/{Name}EventHandlerTest.java`
- JUnit 5 + Mockito
- Mock `AlfrescoEvent` payloads and verify handler logic in isolation

#### 2. Integration Test
`src/test/java/{package}/{Name}IT.java`
- Use an embedded ActiveMQ broker (`@EmbeddedActiveMQ`) or Testcontainers
- Publish a synthetic event, verify the handler processes it correctly
- Assert any side effects (external calls, state changes)

### 3. Update Traceability
Update the Traceability Matrix in `REQUIREMENTS.md` with test references.

## Conventions
- Integration test class name ends with `IT`
- Use `@TestMethodOrder(MethodOrderer.OrderAnnotation.class)` for ordered execution
- Clean up test data in `@AfterAll`
- HTTP test scripts use `curl` with `--fail` and check response codes
