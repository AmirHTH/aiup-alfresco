---
description: "Generate Spring REST controllers, DTOs, and OpenAPI specification for Alfresco extensions."
allowed-tools: "Read, Write, Grep, Glob"
argument-hint: "[path to REQUIREMENTS.md or description]"
---

# /rest-api — REST API Generator

Generate modern REST API code using Spring `@RestController` pattern.

## Input
Read `REQUIREMENTS.md` to identify REST API requirements.

## Output Files

### 1. Controller
`src/main/java/{package}/rest/{Resource}Controller.java`
- Use `@RestController` and `@RequestMapping`
- Inject Alfresco services via `@Autowired`
- Use proper HTTP status codes
- Implement paging for collection endpoints

### 2. DTOs
`src/main/java/{package}/rest/dto/{Resource}Entry.java`
`src/main/java/{package}/rest/dto/{Resource}Paging.java`
- Follow Alfresco paging envelope format
- Use Java records where appropriate (Java 17+)

### 3. OpenAPI Specification
`src/main/resources/openapi/{resource}-api.yaml`
- OpenAPI 3.0 format
- Include request/response schemas matching DTOs
- Document error responses

## Conventions
- Path prefix: `/alfresco/api/-default-/public/{module}/versions/1/`
- Follow REST conventions from CLAUDE.md
- After generation, invoke `rest-api-convention-enforcer` skill

## Validation
Invoke `rest-api-convention-enforcer` skill on generated code.
