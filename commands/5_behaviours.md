---
description: "Scaffold Alfresco behaviour/policy classes with Spring bean wiring."
allowed-tools: "Read, Write, Grep, Glob"
argument-hint: "[path to REQUIREMENTS.md or description]"
---

# /5_behaviours — Behaviour/Policy Generator

Generate Alfresco behaviour classes for node event handling.

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
