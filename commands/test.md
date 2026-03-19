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

### 1. Integration Test
`src/test/java/{package}/{Name}IT.java`
- JUnit 5
- Use Alfresco SDK test runner
- Test content model deployment (type/aspect creation)
- Test Web Script endpoints (if generated)
- Test behaviours (if generated)
- Test actions (if generated)

### 2. HTTP API Test Scripts
`http-tests/{extension-name}.sh`
- Plain shell scripts using `curl`
- Cover: happy path, validation errors (400), not found (404), unauthorized (401)
- Use environment variables for `HOST`, `USERNAME`, `PASSWORD`

### 3. Update Traceability
Update the Traceability Matrix in `REQUIREMENTS.md` with test references.

## Conventions
- Integration test class name ends with `IT`
- Use `@TestMethodOrder(MethodOrderer.OrderAnnotation.class)` for ordered execution
- Clean up test data in `@AfterAll`
- HTTP test scripts use `curl` with `--fail` and check response codes
