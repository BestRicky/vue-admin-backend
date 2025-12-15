# Repository Guidelines

## Project Structure & Module Organization
Shared integration code lives in `common-app/` (client builders, DTOs, auth helpers). Vendor dependencies are checked in under `common/lib/`. `projects/material-reserve/` is the main Spring Boot service; `src/main/java/` hosts domain logic, `src/main/resources/static/` contains the admin UI, and `src/test/java/` stores regression tests. The Tongren workflow REST service now lives directly in `projects/tongren/`, while subscription administration stays in `projects/subscription-admin/`. Reference specifications sit in `docs/`, while `exports/` keeps sample payloads. Runtime artifacts belong in each project’s `var/` directory; keep the root `var/` only as a staging area during migrations.

## Build, Test, and Development Commands
Run `mvn -pl common-app,projects/material-reserve -am clean package` to build the shared library and the material reserve service. Launch the service locally with `mvn -pl projects/material-reserve spring-boot:run` or, after packaging, `java --add-exports=java.base/sun.security.action=ALL-UNNAMED -Dkingdee.config-path=<path>/kingdee-config.properties -jar projects/material-reserve/target/material-reserve-0.5.jar --server.port=18080 --app.kingdee.enabled=true`. The Tongren workflow worker can now be started with `mvn -pl projects/tongren spring-boot:run` (or `java -jar projects/tongren/target/tongren-0.5.jar`). When iterating on static assets, rebuild with `mvn -pl projects/material-reserve -DskipTests package` before redeploying.

## Coding Style & Naming Conventions
Target Java 17 with UTF-8 sources and four-space indentation. Use `UpperCamelCase` for classes, `lowerCamelCase` for methods and fields, and align DTOs with Kingdee payload names. Prefer Lombok annotations already in use (`@Getter`, `@Slf4j`) instead of manual boilerplate. REST controllers should expose `/api/**` routes, and static pages belong under `src/main/resources/static/` with hyphenated filenames.

## Testing Guidelines
Tests rely on JUnit Jupiter and Spring Boot’s test starter. Create mirror packages underneath `src/test/java/` and name test classes `*Test`. Run `mvn -pl projects/material-reserve test` to exercise the suite; the H2 in-memory database boots automatically, so avoid hard-coded ports or credentials. For integration scenarios, add dataset exports under `projects/material-reserve/var/` and document expected outcomes in `projects/material-reserve/docs/`.

## Commit & Pull Request Guidelines
Align commit subjects with the existing `type(scope): statement` pattern (e.g., `feat(reserve): add billing reconciliation`). Write imperative, concise messages and include context in the body when touching multiple modules. Pull requests should summarize the change, list verification steps (`mvn test`, manual job runs), and reference related documents or tickets. Attach relevant screenshots for UI updates and call out configuration impacts so deployment scripts can be updated promptly.

## Configuration & Environment Tips
Maintain deployment secrets in `kingdee-config.properties`; never commit production values. Keep local copies under `/java/config/kingdee-config.properties` or project-specific `.env` files and pass `-Dkingdee.config-path` when running. Check in reusable scripts under `bin/` and prefer project-level `var/` subfolders for logs (`var/logs/`), snapshots, and toolchains, so they travel with the service during packaging.
