# Tools and tech stack

The application is written in Kotlin on the JVM with Ktor and PostgreSQL. Pages are rendered on the server, so there is no separate frontend build in the pipeline. Every choice below was made for one of two reasons: it containerises and runs headless in CI without special handling, or it is the current first-party option in the Kotlin ecosystem.

Versions are pinned in `gradle/libs.versions.toml` and updated through pull requests opened by Dependabot. The versions listed are current as of October 2026.

## Application

| Concern | Choice | Why |
|---|---|---|
| Language | Kotlin 2.4 (K2 compiler) | Null safety and data classes keep the domain model small and explicit. It is the developer's preferred language. |
| Runtime | JDK 25 (LTS), Eclipse Temurin | The current long-term-support release, with official container images. |
| Web framework | Ktor 3.5 (server, Netty engine) | Lightweight and plugin-based, so routing, authentication, sessions and metrics are added explicitly instead of arriving through configuration magic. Its `testApplication` runs the server in-process for tests. |
| HTML | kotlinx.html with HTMX | Type-safe HTML written in Kotlin, rendered on the server. HTMX adds partial page updates for search and forms without a JavaScript build. |
| Persistence | Exposed 1.5 (`exposed-r2dbc`, DSL API) | JetBrains' SQL library for Kotlin, used through its R2DBC module with `suspendTransaction`. The DSL keeps queries visible and type-checked. Version 1.5.1 or later is required, because it fixes an SQL injection advisory published on 10 October 2026. |
| Database driver | R2DBC PostgreSQL driver (`org.postgresql:r2dbc-postgresql`) | Non-blocking database access that fits Ktor's coroutine model. No request thread is held while waiting for the database, and no blocking work has to be moved to a separate dispatcher. |
| Connection pool | r2dbc-pool | The standard pool for R2DBC connections, replacing a JDBC pool such as HikariCP. |
| Migrations | Flyway 13 over JDBC | Versioned SQL migrations run once at start-up, so the schema in every environment is defined by the repository. The double-booking unique partial index is a plain SQL migration. Flyway has no R2DBC support, so the PostgreSQL JDBC driver is kept for this single start-up step only. |
| Database | PostgreSQL 18 | Partial unique indexes and an official container image. It also runs as a service in GitHub Actions. |
| Authentication | Ktor Authentication (form login) and Sessions | Form login with a server-side session referenced by a signed, HttpOnly cookie. |
| Password hashing | bcrypt (`at.favre.lib:bcrypt`), cost 12 | Meets NFR-03 directly. |
| Serialization | kotlinx.serialization | JSON for the API endpoints, used by the end-to-end tests to set up data. |
| CSV export | kotlin-csv | Correct quoting and escaping for US-08, which hand-written string joining tends to get wrong. |
| Configuration | Ktor YAML configuration with environment variable overrides | Satisfies NFR-11: no secrets in the repository, everything supplied through the environment. |
| Logging | Logback with kotlin-logging, JSON output in containers | Structured logs that a log collector can parse. |

### Why R2DBC

Ktor handles requests with coroutines. A JDBC call blocks its thread until the database answers, so every query would have to be wrapped in a blocking dispatcher to avoid starving the request threads. With R2DBC the database call is itself a suspending call, so the whole request path from routing to the database is non-blocking.

The trade-off is maturity. Exposed's R2DBC module is newer than its JDBC module, and Flyway needs a separate JDBC connection. Both are accepted. The application uses only the DSL API, and the JDBC connection exists for a few seconds at start-up.

The database driver also decides how the double-booking guard is detected. A concurrent insert that violates the unique index surfaces as an R2DBC data integrity exception with PostgreSQL error code `23505`. The booking service maps it to the same "slot already taken" refusal as the regular check, and integration test T-05.6 covers it.

## Testing

| Level | Choice | Why |
|---|---|---|
| Unit | kotlin.test on JUnit Platform, with Kotest assertions | The standard Gradle test platform, so CI reports test results without extra configuration. |
| Integration | Ktor `testApplication` with Testcontainers PostgreSQL | Tests run against a real PostgreSQL, so the unique index behind the double-booking rule and the concurrency test (T-05.6) are tested for real, not mocked. |
| End-to-end | Playwright for Java, called from Kotlin | Drives a headless browser against the running container. The comparison with Selenium required by LO7 is written in `docs/decisions/`. |
| Coverage | Kover | JetBrains' Kotlin coverage tool, with reports uploaded as CI artifacts. |
| Load | k6 | A script-based load test for NFR-01, run against the deployed container. |

## Code quality

ktlint enforces formatting and detekt enforces static analysis rules. Both run as Gradle tasks in CI, and a pull request with violations fails its checks.

## Build, delivery and operations

| Concern | Choice | Why |
|---|---|---|
| Build | Gradle 9 with Kotlin DSL and a version catalog | One build language and one place for every dependency version. |
| Container image | Multi-stage Dockerfile: Gradle build stage, `eclipse-temurin:25-jre` runtime stage, non-root user | Every step of the image build is visible in the repository, which matters for explaining the pipeline at the defence. |
| Local environment | Docker Compose (application and PostgreSQL) | Satisfies NFR-09 with one `docker compose up`. It also declares the restart policy and health check for NFR-02. |
| CI/CD | GitHub Actions on a self-hosted runner | Required by the project task (LO5). Uses `actions/setup-java` and `gradle/actions/setup-gradle` for dependency caching. |
| Registry | GitHub Container Registry (GHCR) | Required by the project task (LO6). |
| Metrics | Ktor MicrometerMetrics plugin with a Prometheus registry, exposed at `/metrics` | Request count, latency and error rate for NFR-07 without hand-written instrumentation. |
| Monitoring | Prometheus and Grafana, both in Docker Compose | Proposed for LO9. The comparison with alternatives is written in `docs/decisions/`. |
| Dependency updates | Dependabot for Gradle, Docker and GitHub Actions | The Exposed advisory above shows why dependencies need updating as a process, not as an occasional chore. |
| Secret scanning | GitHub secret scanning and push protection | Supports NFR-11. |

## Development tools

IntelliJ IDEA for Kotlin development, Git with conventional commits, the GitHub CLI for issues and branches, and draw.io for UML diagrams stored in `docs/diagrams/`.
