---
description: "Validates Alfresco content model XML files for correct namespace URI format, mandatory type/aspect declarations, valid property data types, and absence of reserved prefixes (sys:, cm:, app:). Trigger automatically when generating or editing *-model*.xml or *-context.xml files."
user-invocable: false
allowed-tools: "Read, Grep, Glob"
---

# Content Model Validator

Validate the given Alfresco content model XML against these rules:

## Namespace Validation
- Namespace URI must follow the pattern `http://www.{company}.com/model/{prefix}/{version}`
- Namespace prefix must not collide with reserved Alfresco prefixes: `sys`, `cm`, `app`, `usr`, `act`, `wcm`, `wca`, `lnk`, `fm`, `dl`, `ia`, `smf`, `imap`, `emailserver`, `bpm`, `wcmwf`, `trx`, `stcp`
- Prefix must be lowercase alphanumeric, 2-6 characters

## Structure Validation
- Root element must be `<model>` with `name` attribute in format `{prefix}:modelName`
- Must contain `<namespaces>` with at least one `<namespace>` declaration
- If types are declared, they must be inside `<types>` element
- If aspects are declared, they must be inside `<aspects>` element

## Type and Aspect Validation
- Every `<type>` must have a `name` attribute in format `{prefix}:typeName`
- Every `<type>` should declare a `<parent>` (default: `cm:content` or `cm:folder`)
- Property names must use the model prefix: `{prefix}:propertyName`
- Property `<type>` must be a valid Alfresco data type: `d:text`, `d:mltext`, `d:int`, `d:long`, `d:float`, `d:double`, `d:date`, `d:datetime`, `d:boolean`, `d:noderef`, `d:content`, `d:any`, `d:category`, `d:qname`, `d:locale`, `d:period`

## Spring Context Validation
- If a companion `*-context.xml` exists, verify it registers the model via `<bean class="org.alfresco.repo.dictionary.DictionaryBootstrap">` or equivalent
- The `models` property must reference the correct model XML path

## Output
Report all violations with file path, line number, rule violated, and suggested fix. If no violations found, confirm the model is valid.
