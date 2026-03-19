---
description: "Gather and structure requirements for an Alfresco extension as user stories with acceptance criteria."
allowed-tools: "Read, Write, Grep, Glob"
argument-hint: "[description of the extension]"
---

# /requirements — Requirements Gathering

You are helping the user define requirements for an Alfresco Content Services extension.

Given the user's description in "$ARGUMENTS", produce a structured requirements document.

## Output Format

Create a file called `REQUIREMENTS.md` in the project root with:

### 1. Overview
- Extension name
- Business purpose (1-2 sentences)
- Target ACS version (from AGENTS.md or default 26.1)

### 2. User Stories
For each requirement, write a user story:
```
As a [role], I want to [action], so that [benefit].
```

### 3. Acceptance Criteria
For each user story, list testable acceptance criteria:
```
Given [context], when [action], then [expected result].
```

### 4. Content Model Requirements
- Custom types needed (with parent type)
- Custom aspects needed
- Properties for each type/aspect (name, data type, mandatory, constraints)
- Associations (if any)

### 5. API Requirements
- REST endpoints needed (method, path, request/response)
- Web Scripts needed (if any)

### 6. Behaviour Requirements
- Policies/behaviours to trigger on node events
- Actions to register

### 7. Deployment Requirements
- Docker Compose services needed
- Environment-specific configuration

### 8. Traceability Matrix
| Requirement ID | User Story | Content Model | API | Behaviour | Test |
|---------------|------------|---------------|-----|-----------|------|

Leave the Test column empty — it will be filled by `/test`.

## Instructions
- Ask clarifying questions if the description is ambiguous
- Default to Platform JAR packaging; use AMP only when the extension must bundle third-party libraries not already on the Alfresco classpath
- Reference CLAUDE.md conventions for naming and structure
- The output must be complete enough for `/content-model` through `/test` to consume
