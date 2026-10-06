# Glossary

| Term                            | Definition                                                                                                                             |
| ------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| **JHipster**                    | Generator that scaffolds Spring Boot + Angular apps. This repo was generated with 9.0.0-beta.3.                                        |
| **`.yo-rc.json`**               | Yeoman/JHipster config file (under the `generator-jhipster` key) recording the generator choices: app type, auth, databases, entities. |
| **`.jhipster/*.json`**          | Per-entity definitions (fields, validations, relationships) that JHipster uses to generate code.                                       |
| **Monolith**                    | JHipster application type where the backend and frontend ship as one deployable artifact.                                              |
| **`jhipsterSampleApplication`** | The application `baseName`. Java package `io.github.jhipster.sample`.                                                                  |
| **BankAccount**                 | Entity for a user's account, with `name` and `balance`.                                                                                |
| **Operation**                   | Entity for a transaction on a `BankAccount`, with `date`, `description` and `amount`. Can carry many `Label`s.                         |
| **Label**                       | Tag entity attached to operations through the `rel_operation__label` join table.                                                       |
| **`jhi_` prefix**               | Table prefix for built-in tables (`jhi_user`, `jhi_authority`), set by `jhiPrefix`.                                                    |
| **Authority**                   | A role granted to a user, such as `ROLE_ADMIN` or `ROLE_USER` (`security/AuthoritiesConstants.java`).                                  |
| **JWT**                         | JSON Web Token. Issued by `/api/authenticate` and sent as `Authorization: Bearer <token>` for stateless auth.                          |
| **`dev` profile**               | Spring profile for local development: H2 in-memory database, faker data, API docs.                                                     |
| **`prod` profile**              | Spring profile for production: PostgreSQL, minified frontend, static resource caching.                                                 |
| **`api-docs` profile**          | Enables springdoc OpenAPI / Swagger UI.                                                                                                |
| **Faker data**                  | Sample CSV data in `src/main/resources/config/liquibase/fake-data/`, loaded under the `faker` Liquibase context.                       |
| **Liquibase**                   | Database migration tool. `master.xml` includes the ordered changelogs.                                                                 |
| **Ehcache**                     | JCache provider used as the Hibernate second-level cache.                                                                              |
| **HikariCP**                    | JDBC connection pool used by Spring Boot.                                                                                              |
| **`mvnw`**                      | Maven Wrapper for building, testing and packaging the backend (and the bundled frontend in `prod`).                                    |
| **`npmw`**                      | npm wrapper that uses the Node/npm installed locally by Maven.                                                                         |
| **Jib**                         | Maven plugin that builds Docker images without a Dockerfile or Docker daemon.                                                          |
| **Actuator / `/management`**    | Spring Boot operational endpoints: health, metrics, prometheus, logs, configuration.                                                   |
| **Resource**                    | JHipster term for a REST controller, such as `BankAccountResource`.                                                                    |
| **VM (`web/rest/vm`)**          | View models used as request bodies (`LoginVM`, `ManagedUserVM`, `KeyAndPasswordVM`).                                                   |
| **Route resolver**              | Angular function that pre-fetches an entity before a route activates.                                                                  |
| **`UserRouteAccessService`**    | Angular route guard that checks authentication and required authorities.                                                               |
| **`jhiTranslate`**              | Angular directive for translated text through ngx-translate.                                                                           |
| **Vitest**                      | Frontend unit test runner.                                                                                                             |
| **Gatling**                     | Load-testing tool. Simulations live in `src/test/java/gatling/simulations/`.                                                           |
| **Cypress**                     | Browser end-to-end test framework.                                                                                                     |
| **ArchUnit**                    | Library behind `TechnicalStructureTest`, which enforces package layering.                                                              |
| **JHipster Control Center**     | Optional management UI (`src/main/docker/jhipster-control-center.yml`).                                                                |
