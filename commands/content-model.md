---
description: "Generate Alfresco content model XML and Spring context file from requirements."
allowed-tools: "Read, Write, Grep, Glob"
argument-hint: "[path to REQUIREMENTS.md or description]"
---

# /content-model — Content Model Generator

Generate Alfresco content model files based on requirements.

## Input
Read `REQUIREMENTS.md` (or use "$ARGUMENTS" as input) to extract content model requirements and
resolve the Platform JAR project's `Root path` from Section 2 (Project Architecture).

- If Section 2 contains no `Platform JAR` project, stop and explain that `/content-model` only
  applies to the in-process repository addon project.

## Output Files

### 1. Content Model XML
Create `{platform-project-root}/src/main/resources/alfresco/module/{module-id}/model/content-model.xml`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<model name="{prefix}:contentModel" xmlns="http://www.alfresco.org/model/dictionary/1.0">
    <description>...</description>
    <version>1.0</version>
    <imports>
        <import uri="http://www.alfresco.org/model/dictionary/1.0" prefix="d"/>
        <import uri="http://www.alfresco.org/model/content/1.0" prefix="cm"/>
    </imports>
    <namespaces>
        <namespace uri="http://www.{company}.com/model/{prefix}/1.0" prefix="{prefix}"/>
    </namespaces>
    <!-- types and aspects here -->
</model>
```

### 2. Spring Context
Create `{platform-project-root}/src/main/resources/alfresco/module/{module-id}/context/bootstrap-context.xml`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" ...>
    <bean id="{prefix}.dictionaryBootstrap"
          parent="dictionaryModelBootstrap"
          depends-on="dictionaryBootstrap">
        <property name="models">
            <list>
                <value>alfresco/module/{module-id}/model/content-model.xml</value>
            </list>
        </property>
    </bean>
</beans>
```

## Conventions
- `{platform-project-root}` is `.` for Platform JAR only mode, or `{name}-platform/` for Mixed mode
- Follow namespace naming from CLAUDE.md
- Use `cm:content` as default parent for document types
- Use `cm:folder` as default parent for folder types
- Every property must specify a valid `d:` data type
- Include constraints where requirements specify them
- Never generate content model files inside the Event Handler project

## Validation
After generating files, invoke the `content-model-validator` skill to validate the output.
