# Getting Started

## Prerequisites

- Java 21
- Node.js is optional. The Maven build installs the version pinned in `pom.xml` (`node.version` v24.13.0, `npm.version` 11.8.0), and `./npmw` uses that local copy.
- Docker is optional for dev. You need it for PostgreSQL, Sonar, monitoring and container builds.

## Build System

The repo ships two wrappers so you don't need global installs:

| Wrapper               | Purpose                                                                                                                                                                                           |
| --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `./mvnw` (`mvnw.cmd`) | Maven Wrapper: backend dependencies, compilation, tests, packaging. Config lives in `pom.xml`, with profiles `dev`, `prod`, `api-docs`, `tls`, `war`, `webapp`, `e2e`, `no-liquibase` and others. |
| `./npmw` (`npmw.cmd`) | npm wrapper that uses the Node/npm installed locally by the Maven build. It runs the scripts in `package.json`.                                                                                   |

Install frontend dependencies (re-run only when `package.json` changes):

```bash
./npmw install
```

Run `./npmw run` to list all available scripts.

## Running the Application

For development, run the backend and frontend in two terminals so the browser auto-reloads on changes:

```bash
./npmw run backend:start   # Spring Boot on http://localhost:8080 (dev profile, H2)
./npmw run start           # Angular dev server (ng serve --hmr) proxying API calls to 8080
```

- `./npmw run watch` starts both together using `concurrently`.
- `./npmw run backend:start` runs `./mvnw` with frontend build steps skipped. The default Maven goal runs `spring-boot:run` with the `dev` profile.
- The Angular dev server port is set in `angular.json` (`9060`). `webpack/webpack.custom.js` adds BrowserSync on port 9000, and `webpack/proxy.conf.js` proxies `/api`, `/management` and similar paths to the backend.
- Default users (from `src/main/resources/config/liquibase/data/user.csv`) are `admin`/`admin` and `user`/`user`.
- In dev, the H2 console is available at `http://localhost:8080/h2-console`.

## Production Build

```bash
./mvnw -Pprod clean verify
java -jar target/*.jar
```

This bundles the minified Angular app into the jar. Then open http://localhost:8080. See [Deployment](deployment.md) for WAR and Docker options.

## Useful Scripts

| Script                    | Command                                                |
| ------------------------- | ------------------------------------------------------ |
| `./npmw run lint`         | ESLint over the repo                                   |
| `./npmw test`             | Lint, then Vitest unit tests with coverage             |
| `./mvnw verify`           | Backend unit and integration tests                     |
| `./npmw run docker:db:up` | Start PostgreSQL from `src/main/docker/postgresql.yml` |
| `./npmw run java:docker`  | Build a Docker image with Jib                          |
