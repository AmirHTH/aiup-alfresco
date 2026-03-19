---
description: "Checks REST path structure, HTTP method semantics, and paging envelope format against Alfresco conventions. Trigger when generating or reviewing REST controllers."
user-invocable: false
allowed-tools: "Read, Grep, Glob"
---

# REST API Convention Enforcer

Validate REST API code against Alfresco REST API conventions.

## Path Conventions
- Custom endpoints: `/alfresco/api/-default-/public/{module}/versions/1/{resource}`
- Or: `/alfresco/s/api/{resource}` for Web Script-based APIs
- Resource names must be plural nouns in kebab-case
- No verbs in paths (use HTTP methods instead)

## HTTP Method Semantics
- `GET` — read, must be idempotent, no request body
- `POST` — create, returns 201 with Location header
- `PUT` — full update, returns 200
- `DELETE` — remove, returns 204 (no body)
- `PATCH` — partial update (avoid if possible in Alfresco context)

## Paging Envelope
Collection responses must use the Alfresco paging envelope:
```json
{
  "list": {
    "pagination": {
      "count": 10,
      "hasMoreItems": true,
      "totalItems": 42,
      "skipCount": 0,
      "maxItems": 10
    },
    "entries": [...]
  }
}
```

## Error Response Format
Errors must follow:
```json
{
  "error": {
    "statusCode": 400,
    "briefSummary": "...",
    "descriptionURL": "...",
    "stackTrace": "..."
  }
}
```

## Output
Report all convention violations with file, line, and suggested fix.
