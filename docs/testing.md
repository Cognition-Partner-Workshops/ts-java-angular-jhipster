# Testing

| Layer                        | Framework                                    | Location                                   | Command               |
| ---------------------------- | -------------------------------------------- | ------------------------------------------ | --------------------- |
| Backend unit and integration | JUnit 5, Spring Boot Test, MockMvc, ArchUnit | `src/test/java/io/github/jhipster/sample/` | `./mvnw verify`       |
| Frontend unit                | Vitest + Angular `TestBed`                   | `src/main/webapp/app/**/*.spec.ts`         | `./npmw test`         |
| Performance                  | Gatling (Java DSL)                           | `src/test/java/gatling/simulations/`       | `./mvnw gatling:test` |
| End-to-end                   | Cypress                                      | `src/test/javascript/cypress/`             | `./npmw run e2e`      |

## Backend Integration Tests

- `src/test/java/io/github/jhipster/sample/IntegrationTest.java` is a meta-annotation that combines `@SpringBootTest` (the app plus `JacksonConfiguration`, `AsyncSyncConfiguration` and `JacksonHibernateConfiguration`) with `@EmbeddedSQL`.
- `config/EmbeddedSQL.java`, `SqlTestContainer.java`, `DatabaseTestcontainer.java` and `SqlTestContainersSpringContextCustomizerFactory.java` provide the test database. With the default Maven `dev` profile, tests run with `test,testdev` (H2 in-memory). With `-Pprod`, they run with `test,testprod`, which starts a PostgreSQL Testcontainer. Config files are `src/test/resources/config/application-testdev.yml` and `application-testprod.yml`.
- `web/rest/*IT.java` tests (`BankAccountResourceIT`, `OperationResourceIT`, `LabelResourceIT`, `UserResourceIT`, `AccountResourceIT`, `AuthenticateControllerIT`, and others) use `@AutoConfigureMockMvc`, `@WithMockUser` and `@Transactional` to exercise the REST endpoints without a running server.
- `domain/*Test.java`, `*Asserts.java` and `*TestSamples.java` provide entity equality tests and shared assertions and fixtures.
- `TechnicalStructureTest.java` uses ArchUnit `layeredArchitecture()` to enforce the `Config` / `Web` / `Service` / `Security` / `Persistence` / `Domain` layering.
- Other suites: `security/` (`DomainUserDetailsServiceIT`, `SecurityUtilsUnitTest`), `management/SecurityMetersServiceTests`, `config/timezone/HibernateTimeZoneIT`.
- `./npmw run backend:unit:test` runs the same suite with quiet logging. CI uses `./npmw run ci:backend:test`, which adds javadoc and checkstyle checks.

## Frontend Unit Tests (Vitest)

- Vitest is configured through the `@angular/build:unit-test` builder in `angular.json` and through `vitest-base.config.ts`. Coverage goes to `target/test-results/`.
- Spec files sit next to their components and services, for example `app/entities/operation/route/operation-routing-resolve.service.spec.ts` and `app/core/auth/account.service.spec.ts`.
- Import test APIs from `vitest` (for example `vitest.spyOn`), not Jest.
- `./npmw test` runs ESLint first (`pretest`) and then `ng test --coverage`. `./npmw run test:watch` runs in watch mode.

## Gatling Performance Tests

- `BankAccountGatlingTest.java`, `LabelGatlingTest.java` and `OperationGatlingTest.java` in `src/test/java/gatling/simulations/` use the Gatling Java API (`io.gatling.javaapi.*`).
- Each simulation authenticates, then ramps up users who run a create / read / delete cycle against the entity's REST endpoints.
- Start the application, then run `./mvnw gatling:test`.

## Cypress E2E Tests

- Config is in `cypress.config.ts` and `cypress-audits.config.ts`. Specs are in `src/test/javascript/cypress/e2e/`:
  - `account/`: login, logout, register, settings, password, password reset
  - `administration/administration.cy.ts`
  - `entity/`: `bank-account`, `label`, `operation`, `authority`
- Shared commands are in `support/commands.ts`, `support/account.ts` and `support/entity.ts`.
- To run them, start the app with `./npmw run app:start`, then run `./npmw run e2e` (headed) or `./npmw run e2e:headless`. Override the login with `CYPRESS_E2E_USERNAME` / `CYPRESS_E2E_PASSWORD`.
- Lighthouse audits (`e2e/lighthouse.audits.ts`) run with `./npmw run e2e:cypress:audits` against a `prod` build. The report is written to `target/cypress/lhreport.html`.
