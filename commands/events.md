---
description: "Generate an Out-of-Process Spring Boot event listener for Alfresco Java Event API."
allowed-tools: "Read, Write, Grep, Glob"
argument-hint: "[event type or description]"
---

# /events — Out-of-Process Event Listener Generator

Generate a Spring Boot Out-of-Process application that consumes Alfresco repository events via ActiveMQ.

## When to use
Use this command for **asynchronous, reactive integrations**: notifications, external system sync, workflow triggers based on content changes.

Do NOT use for:
- Synchronous content validation → use `/behaviours` (In-Process)
- REST API exposure → use `/web-scripts` (In-Process)
- On-demand triggered logic → use `/actions` (In-Process)

## Input
Read `REQUIREMENTS.md` to identify event-driven integration requirements.

## Output Files

### 1. Spring Boot Application
`src/main/java/{package}/{Name}Application.java`
- `@SpringBootApplication`

### 2. Event Handler
`src/main/java/{package}/handler/{Name}EventHandler.java`
- Annotate with `@AlfrescoEventListener`
- Filter by node type or aspect where applicable
- Log at `INFO` level on successful processing; `ERROR` on failure
- Configure a dead-letter queue for error handling

### 3. Application Properties
`src/main/resources/application.properties`
```properties
spring.activemq.broker-url=tcp://localhost:61616
spring.activemq.user=${ACTIVEMQ_USER}
spring.activemq.password=${ACTIVEMQ_PASSWORD}
alfresco.events.topic=alfresco.repo.event2
```

### 4. POM
`pom.xml` with parent:
```xml
<parent>
    <groupId>org.alfresco</groupId>
    <artifactId>alfresco-java-sdk</artifactId>
    <version>7.2.0</version>
</parent>
```
Include `alfresco-java-event-api-spring-boot-starter` dependency.

## Conventions
- Consumer group naming: `{prefix}.{purpose}` — e.g. `acme.invoiceProcessor`
- Always configure a dead-letter queue
- Use type/aspect filters to avoid processing unrelated events
- After generation, invoke `event-api-topology-checker` skill

## Deployment
The Out-of-Process app runs as a **separate service** in `compose.yaml` alongside ACS:
```yaml
{extension-name}:
  build: .
  environment:
    SPRING_ACTIVEMQ_USER: ${ACTIVEMQ_USER}
    SPRING_ACTIVEMQ_PASSWORD: ${ACTIVEMQ_PASSWORD}
  depends_on:
    activemq:
      condition: service_healthy
    alfresco:
      condition: service_healthy
```
After generation, run `/docker-compose` to add this service to the stack.
