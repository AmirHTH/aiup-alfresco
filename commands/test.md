---
description: "Generate integration tests for Alfresco extensions using Testcontainers (self-contained, no pre-running ACS required)."
allowed-tools: "Read, Write, Grep, Glob"
argument-hint: "[path to REQUIREMENTS.md or description]"
---

# /test — Test Generator

Generate tests for the Alfresco extension.

## Input
Read `REQUIREMENTS.md` and all generated artefacts from previous commands.
Resolve the project `Root path` values from Section 2 (Project Architecture) before writing tests.

- Generate Platform JAR tests only when a `Platform JAR` project exists.
- Generate Event Handler tests only when an `Event Handler` project exists.
- In Mixed mode, write each test file under its own project root; do not place both test suites in
  the same module.

## Output Files

### In-Process SDK (Maven) — Platform JAR / AMP

#### 1. Testcontainers Integration Test
`{platform-project-root}/src/test/java/{package}/{Name}ContainerIT.java`

Self-contained: starts the ACS stack from `compose.yaml` via `DockerComposeContainer`, runs all
scenarios, then tears everything down. No pre-running ACS instance required.

```java
@Testcontainers
@TestInstance(TestInstance.Lifecycle.PER_CLASS)
@TestMethodOrder(MethodOrderer.OrderAnnotation.class)
public class {Name}ContainerIT {

    @Container
    static final DockerComposeContainer<?> STACK =
            new DockerComposeContainer<>(new File("{compose-file-relative-path}"))
                    .withExposedService("alfresco_1", 8080,
                            Wait.forHttp("/alfresco/api/-default-/public/alfresco/versions/1/probes/-ready-")
                                    .withStartupTimeout(Duration.ofMinutes(10)))
                    .withLocalCompose(true);

    private String nodesApi;
    private String authHeader;
    private HttpClient http;

    @BeforeAll
    void setup() throws Exception {
        String host = STACK.getServiceHost("alfresco_1", 8080);
        int    port = STACK.getServicePort("alfresco_1", 8080);
        nodesApi   = "http://" + host + ":" + port
                   + "/alfresco/api/-default-/public/alfresco/versions/1/nodes";
        authHeader = "Basic " + Base64.getEncoder()
                .encodeToString("admin:admin".getBytes(StandardCharsets.UTF_8));
        http = HttpClient.newHttpClient();
        // create any shared test folder structure here
    }

    @AfterAll
    void cleanup() throws Exception {
        // permanently delete all test data
    }

    // @Test methods using java.net.http.HttpClient against nodesApi
}
```

- Use `java.net.http.HttpClient` for HTTP calls — no extra test dependencies.
- Use `@TestInstance(PER_CLASS)` + `@Order` when tests share state (e.g., a node created in test 1 is used in test 2).
- Cover all user stories and acceptance criteria from `REQUIREMENTS.md`.
- Create isolated test folder trees in `@BeforeAll`; permanently delete them in `@AfterAll`.

#### 2. External ACS Integration Test (optional)
`{platform-project-root}/src/test/java/{package}/{Name}IT.java`

Runs against an already-running ACS instance when `-Dacs.endpoint.path=` is provided.
Skips gracefully otherwise so `mvn verify` always succeeds without Docker.

```java
@BeforeAll
void setup() throws Exception {
    assumeTrue(System.getProperty("acs.endpoint.path") != null,
            "Skipping: set -Dacs.endpoint.path=http://host:8080 to run against an external ACS instance");
    // ...
}
```

#### 3. HTTP API Smoke Tests
`{platform-project-root}/http-tests/{extension-name}.sh`
- Plain shell scripts using `curl` + `jq`
- Cover: happy path, validation errors (400), not found (404), unauthorized (401)
- Use environment variables for `HOST`, `USERNAME`, `PASSWORD`
- Auto-detect `sha256sum` vs `shasum` where content hashing is needed

#### 4. Maven Failsafe + Testcontainers configuration

Add to `{platform-project-root}/pom.xml`:

```xml
<properties>
    <testcontainers.version>1.19.8</testcontainers.version>
    <maven.failsafe.plugin.version>3.2.5</maven.failsafe.plugin.version>
</properties>

<dependencyManagement>
    <dependencies>
        <!-- existing Alfresco BOM ... -->
        <dependency>
            <groupId>org.testcontainers</groupId>
            <artifactId>testcontainers-bom</artifactId>
            <version>${testcontainers.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>

<dependencies>
    <!-- existing deps ... -->
    <dependency>
        <groupId>org.testcontainers</groupId>
        <artifactId>testcontainers</artifactId>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.testcontainers</groupId>
        <artifactId>junit-jupiter</artifactId>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-simple</artifactId>
        <version>2.0.13</version>
        <scope>test</scope>
    </dependency>
</dependencies>

<build>
    <plugins>
        <!-- Surefire: unit tests only -->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-surefire-plugin</artifactId>
            <configuration>
                <excludes><exclude>**/*IT.java</exclude></excludes>
            </configuration>
        </plugin>
        <!-- Failsafe: IT classes (Testcontainers + optional external) -->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-failsafe-plugin</artifactId>
            <version>${maven.failsafe.plugin.version}</version>
            <executions>
                <execution>
                    <goals>
                        <goal>integration-test</goal>
                        <goal>verify</goal>
                    </goals>
                </execution>
            </executions>
            <configuration>
                <includes><include>**/*IT.java</include></includes>
                <systemPropertyVariables>
                    <acs.endpoint.path>${acs.endpoint.path}</acs.endpoint.path>
                    <acs.username>${acs.username}</acs.username>
                    <acs.password>${acs.password}</acs.password>
                </systemPropertyVariables>
            </configuration>
        </plugin>
    </plugins>
</build>
```

**Run commands**:
```bash
# Self-contained (Docker required, port 8080 must be free)
mvn verify

# Against a running stack
mvn verify -Dacs.endpoint.path=http://localhost:8080

# Skip container tests, only build
mvn package -DskipITs
```

> **Prerequisite**: `compose.yaml` must exist at the repository root with the ACS stack and a volume mount
> for the extension JAR. Run `/docker-compose` first if it has not been generated yet.

---

### Out-of-Process SDK (Spring Boot) — Event Listeners

#### 1. Unit Tests
`{event-project-root}/src/test/java/{package}/handler/{Name}EventHandlerTest.java`
- JUnit 5 + Mockito
- Mock `AlfrescoEvent` payloads and verify handler logic in isolation

#### 2. Integration Test
`{event-project-root}/src/test/java/{package}/{Name}IT.java`
- Use an embedded ActiveMQ broker (`@EmbeddedActiveMQ`) or Testcontainers
- Publish a synthetic event, verify the handler processes it correctly
- Assert any side effects (external calls, state changes)

---

### Update Traceability
Update the Traceability Matrix in `REQUIREMENTS.md` with test references pointing to the
generated test class and method names.

## Conventions
- `{platform-project-root}` is `.` for Platform JAR only mode, or `{name}-platform/` for Mixed mode
- `{event-project-root}` is `.` for Event Handler only mode, or `{name}-events/` for Mixed mode
- `{compose-file-relative-path}` is `compose.yaml` for single-project layouts and typically `../compose.yaml` for child modules in Mixed mode
- Integration test class name ends with `IT`
- `@TestMethodOrder(MethodOrderer.OrderAnnotation.class)` for ordered execution
- Clean up test data in `@AfterAll` using `?permanent=true` deletes
- HTTP test scripts set `set -euo pipefail` and report pass/fail counts
