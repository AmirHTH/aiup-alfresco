---
description: "Generate Alfresco content model XML and Spring context file from requirements."
allowed-tools: "Read, Write, Grep, Glob"
argument-hint: "[path to REQUIREMENTS.md or description]"
---

# /content-model — Content Model Generator

Generate Alfresco content model files based on requirements.

## Input
Read `REQUIREMENTS.md` (or use "$ARGUMENTS" as input) to extract content model requirements.

## Output Files

### 1. Content Model XML
Create `src/main/resources/alfresco/module/{module-id}/model/content-model.xml`:

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
Create `src/main/resources/alfresco/module/{module-id}/context/bootstrap-context.xml`:

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
- Follow namespace naming from CLAUDE.md
- Use `cm:content` as default parent for document types
- Use `cm:folder` as default parent for folder types
- Every property must specify a valid `d:` data type
- Include constraints where requirements specify them

## Validation
After generating files, invoke the `content-model-validator` skill to validate the output.
