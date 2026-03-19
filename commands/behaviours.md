---
description: "Scaffold Alfresco behaviour/policy classes with Spring bean wiring. In-Process SDK (Maven) only."
allowed-tools: "Read, Write, Grep, Glob"
argument-hint: "[path to REQUIREMENTS.md or description]"
---

# /behaviours — Behaviour/Policy Generator

> **In-Process SDK only** — deploys inside the ACS JVM as a Platform JAR.
> For asynchronous event-driven reactions to repository changes from an external app, use `/events` (Out-of-Process) instead.

Generate Alfresco behaviour classes for synchronous node event handling.

## Input
Read `REQUIREMENTS.md` to identify behaviour requirements.

## Output Files

### 1. Behaviour Class
`src/main/java/{package}/behaviour/{Name}Behaviour.java`
- Implement appropriate policy interface (`OnCreateNodePolicy`, `OnUpdatePropertiesPolicy`, `OnAddAspectPolicy`, etc.)
- Register behaviour in `init()` method using `PolicyComponent`
- Inject required Alfresco services

### 2. Spring Bean Configuration
Add bean definition to `src/main/resources/alfresco/module/{module-id}/context/service-context.xml`

## Conventions
- Use `JavaBehaviour` with `NotificationFrequency.TRANSACTION_COMMIT` (default) or `EVERY_EVENT` where specified
- Bind to specific types/aspects from the content model
- Log at appropriate levels
- Handle transaction context properly
