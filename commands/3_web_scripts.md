---
description: "Generate Alfresco Web Script descriptors, controllers, and FreeMarker templates."
allowed-tools: "Read, Write, Grep, Glob"
argument-hint: "[path to REQUIREMENTS.md or description]"
---

# /3_web_scripts — Web Script Generator

Generate Web Script files from requirements.

## Input
Read `REQUIREMENTS.md` to identify API requirements suitable for Web Scripts.

## Output Files (per Web Script)

### 1. Descriptor
`src/main/resources/alfresco/extension/templates/webscripts/{path}/{name}.{method}.desc.xml`

### 2. Controller (Java or JavaScript)
- Java: `src/main/java/{package}/{Name}WebScript.java` extending `DeclarativeWebScript`
- JavaScript: `src/main/resources/alfresco/extension/templates/webscripts/{path}/{name}.{method}.js`

### 3. Response Template
`src/main/resources/alfresco/extension/templates/webscripts/{path}/{name}.{method}.json.ftl`

## Conventions
- URL pattern: `/api/{extension-prefix}/{resource}`
- Authentication: `user` (default) or `admin` where specified
- Format: `json` (default)
- Transaction: `required` for write operations, `none` for read-only
- Follow CLAUDE.md REST path conventions
