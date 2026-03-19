---
description: "Generate integration test classes and Postman collections for Alfresco extensions."
allowed-tools: "Read, Write, Grep, Glob"
argument-hint: "[path to REQUIREMENTS.md or description]"
---

# /8_test — Test Generator

Generate tests for the Alfresco extension.

## Input
Read `REQUIREMENTS.md` and all generated artefacts from previous commands.

## Output Files

### 1. Integration Test
`src/test/java/{package}/{Name}IT.java`
- JUnit 5
- Use Alfresco SDK test runner
- Test content model deployment (type/aspect creation)
- Test REST endpoints (if generated)
- Test behaviours (if generated)
- Test actions (if generated)

### 2. Postman Collection
`postman/{extension-name}.postman_collection.json`
- Newman-ready (can run in CI)
- Include environment variables for `host`, `username`, `password`
- Test each REST endpoint with:
  - Happy path
  - Validation errors (400)
  - Not found (404)
  - Unauthorized (401)

### 3. Update Traceability
Update the Traceability Matrix in `REQUIREMENTS.md` with test references.

## Conventions
- Integration test class name ends with `IT`
- Use `@TestMethodOrder(MethodOrderer.OrderAnnotation.class)` for ordered execution
- Clean up test data in `@AfterAll`
- Postman tests use `pm.test()` assertions
