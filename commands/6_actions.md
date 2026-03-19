---
description: "Scaffold Alfresco ActionExecuter classes with Spring bean registration."
allowed-tools: "Read, Write, Grep, Glob"
argument-hint: "[path to REQUIREMENTS.md or description]"
---

# /6_actions — Action Executor Generator

Generate Alfresco action classes.

## Input
Read `REQUIREMENTS.md` to identify action requirements.

## Output Files

### 1. Action Class
`src/main/java/{package}/action/{Name}ActionExecuter.java`
- Extend `ActionExecuterAbstractBase`
- Implement `executeImpl()` method
- Define parameters via `addParameterDefinitions()`

### 2. Spring Bean Registration
Add to `src/main/resources/alfresco/module/{module-id}/context/service-context.xml`:
```xml
<bean id="{prefix}.{actionName}" class="{package}.action.{Name}ActionExecuter" parent="action-executer">
    <property name="nodeService" ref="NodeService"/>
    <!-- additional service references -->
</bean>
```

## Conventions
- Action name format: `{prefix}-{action-name}`
- Use parent `action-executer` bean
- Define compensation action if the operation is reversible
