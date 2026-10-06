# jhipsterSampleApplication Documentation

`jhipsterSampleApplication` is a full-stack monolithic web application generated with JHipster 9.0.0-beta.3. It pairs a Spring Boot backend (`src/main/java/io/github/jhipster/sample/`) with an Angular frontend (`src/main/webapp/`), uses stateless JWT authentication, and manages a small banking domain: bank accounts, the operations recorded against them, and labels used to tag those operations.

These pages are high-level summaries. For generator settings see `.yo-rc.json`, and for the full command reference see the root [`README.md`](../README.md).

## Contents

| Page                                                  | What it covers                                                                       |
| ----------------------------------------------------- | ------------------------------------------------------------------------------------ |
| [Overview](overview.md)                               | What the app is, tech stack, domain entities, architecture diagrams, Spring profiles |
| [Getting Started](getting-started.md)                 | Maven/npm wrappers and running the app locally                                       |
| [Backend Architecture](backend-architecture.md)       | Configuration, JWT security, domain model, Liquibase schema, REST API                |
| [Frontend Architecture](frontend-architecture.md)     | Angular bootstrap, navigation, i18n, routing and resolvers                           |
| [Testing](testing.md)                                 | Backend integration tests, Vitest, Gatling, Cypress                                  |
| [Development Environment](development-environment.md) | Dev container, Docker services, monitoring and code quality                          |
| [Deployment](deployment.md)                           | Production build and Docker deployment                                               |
| [Glossary](glossary.md)                               | Project and JHipster terms                                                           |
