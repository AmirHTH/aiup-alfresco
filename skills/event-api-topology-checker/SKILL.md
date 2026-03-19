---
description: "Validates ActiveMQ topic names and consumer group patterns against Alfresco event API conventions. Trigger when generating event-driven code."
user-invocable: false
allowed-tools: "Read, Grep"
---

# Event API Topology Checker

Validate event-driven code against Alfresco Java Event API conventions.

## Topic Naming
- Default Alfresco topic: `alfresco.repo.event2`
- Custom topics must not collide with built-in topics
- Consumer group names should follow: `{extension-prefix}.{purpose}`

## Event Types
Valid event types from `org.alfresco.event.sdk.model.v1`:
- `NodeCreatedEvent`, `NodeUpdatedEvent`, `NodeDeletedEvent`
- `ContentCreatedEvent`, `ContentUpdatedEvent`, `ContentDeletedEvent`
- `ChildAssocCreatedEvent`, `ChildAssocDeletedEvent`
- `PeerAssocCreatedEvent`, `PeerAssocDeletedEvent`

## Configuration Validation
- Verify `spring.activemq.broker-url` is set
- Verify event filters match expected node types/aspects
- Warn if no error handling or dead-letter queue is configured

## Output
Report topology issues, naming violations, and missing error handling.
