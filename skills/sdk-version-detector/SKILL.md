---
description: "Reads pom.xml or build.gradle, detects Alfresco SDK version, and adjusts generated code accordingly. Trigger when generating Java code for Alfresco extensions."
user-invocable: false
allowed-tools: "Read, Grep, Glob"
---

# SDK Version Detector

Detect the Alfresco SDK version from the project build files and report which code generation patterns to use.

## Detection Logic

1. Search for `pom.xml` files in the project
2. Look for `alfresco-sdk-parent` or `alfresco.sdk.version` property
3. Look for `alfresco.platform.version` or `acs.version` property
4. If `build.gradle` exists, check for `org.alfresco:alfresco-sdk` dependency

## Version Mapping

| SDK Version | ACS Version | Java | Spring | Key Differences |
|-------------|-------------|------|--------|-----------------|
| 4.x (≤4.11) | 6.x–7.x | 11 | 5.x | Legacy `ServiceRegistry`, `AuthenticationUtil`, XML bean config |
| 4.x (4.12+) | 25.x–26.1 | 17+ | 6.x/Boot 3.x | Web Scripts, XML for Alfresco integration points, Java config for internal wiring |

## Output

Report:
- Detected SDK version
- Detected ACS version
- Java version requirement
- Whether to use XML or Java-based Spring configuration
- Whether to target Web Scripts or REST API controllers
- Any version-specific warnings
