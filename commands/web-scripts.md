---
description: "Generate Alfresco Web Script descriptors, controllers, and FreeMarker templates. In-Process SDK (Maven) only."
allowed-tools: "Read, Write, Grep, Glob"
argument-hint: "[path to REQUIREMENTS.md or description]"
---

# /web-scripts — Web Script Generator

> **In-Process SDK only** — deploys inside the ACS JVM as a Platform JAR.

Generate Web Script files from requirements.

## Input
Read `REQUIREMENTS.md` to identify API requirements suitable for Web Scripts and resolve the
Platform JAR project's `Root path` from Section 2 (Project Architecture).

- If Section 2 contains no `Platform JAR` project, stop and explain that `/web-scripts`
  only applies to the in-process repository addon project.

## Output Files (per Web Script)

### 1. Descriptor
`{platform-project-root}/src/main/resources/alfresco/extension/templates/webscripts/{path}/{name}.{method}.desc.xml`

### 2. Controller (Java or JavaScript)
- Java: `{platform-project-root}/src/main/java/{package}/{Name}WebScript.java` extending `DeclarativeWebScript`
- JavaScript: `{platform-project-root}/src/main/resources/alfresco/extension/templates/webscripts/{path}/{name}.{method}.js`

### 3. Response Template
`{platform-project-root}/src/main/resources/alfresco/extension/templates/webscripts/{path}/{name}.{method}.json.ftl`

## Conventions
- `{platform-project-root}` is `.` for Platform JAR only mode, or `{name}-platform/` for Mixed mode
- URL pattern: `/api/{extension-prefix}/{resource}`
- Authentication: `user` (default) or `admin` where specified
- Format: `json` (default)
- Transaction: `required` for write operations, `none` for read-only
- Follow CLAUDE.md REST path conventions
- Never generate Web Scripts inside the Event Handler project
