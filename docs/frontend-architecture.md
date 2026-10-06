# Frontend Architecture

The client is an Angular 21 standalone-component app in `src/main/webapp/`. It's built with the Angular CLI and custom webpack config (`angular.json`, `webpack/webpack.custom.js`), styled with Bootstrap 5 and Sass, and served by Spring Boot in production.

| Path                                                   | Contents                                                                                              |
| ------------------------------------------------------ | ----------------------------------------------------------------------------------------------------- |
| `src/main/webapp/main.ts`, `bootstrap.ts`              | Entry point                                                                                           |
| `app/app.ts`, `app/app.config.ts`, `app/app.routes.ts` | Root component, providers, top-level routes                                                           |
| `app/core/`                                            | Auth (`AccountService`, `AuthServerProvider`, `UserRouteAccessService`), HTTP interceptors, utilities |
| `app/shared/`                                          | Alerts, `*jhiHasAnyAuthority`, date pipes, language, pagination, sort                                 |
| `app/layouts/`                                         | `main`, `navbar`, `footer`, `error`, `profiles`                                                       |
| `app/entities/`                                        | `bank-account`, `label`, `operation`, `admin/authority`, `user`                                       |
| `app/admin/`                                           | User management, metrics, health, configuration, logs, API docs                                       |
| `app/account/`                                         | Register, activate, settings, password, password reset                                                |
| `app/home/`, `app/login/`                              | Home and login pages                                                                                  |
| `i18n/en/`                                             | Translation bundles                                                                                   |

## Entry Point

1. `main.ts` dynamically imports `bootstrap.ts`.
2. `bootstrap.ts` calls `enableProdMode()` when `DEBUG_INFO_ENABLED` is false, then runs `bootstrapApplication(App, appConfig)`.
3. `app/app.config.ts` registers the router (component input binding, a navigation error handler that redirects 401/403/404 errors, and debug tracing in dev), `TranslationModule`, `LOCALE_ID`, the `NgbDateDayjsAdapter`, `AppPageTitleStrategy`, the disabled `ServiceWorkerModule` (PWA), and the HTTP interceptors:
   - `authInterceptor` adds `Authorization: Bearer <token>`
   - `authExpiredInterceptor` handles 401s
   - `errorHandlerInterceptor`
   - `notificationInterceptor`
4. `app/app.ts` renders `<jhi-main />`. `layouts/main/main.ts` calls `AccountService.identity()` to restore the session, and on language change it updates the page title and the `dayjs` locale.

## Navigation

`app/layouts/navbar/navbar.ts` and `navbar.html` render the top menu as a named router outlet (`outlet: 'navbar'` in `app.routes.ts`):

- Home
- **Entities**: Bank Account, Label, Operation, Authority
- **Administration** (`ROLE_ADMIN` only, via `*jhiHasAnyAuthority`): User management, Metrics, Health, Configuration, Logs, API (docs), Database (H2 console in dev)
- **Account**: Settings, Password, Sign out, or Sign in / Register when anonymous

`active-menu.directive.ts` highlights the current section. `layouts/profiles/` shows a ribbon for the active Spring profile.

## Internationalization

- `ngx-translate` is configured in `app/shared/language/translation.module.ts`. It loads `./i18n/<lang>.json` over HTTP (with a cache-busting `I18N_HASH`), falls back to `en`, and restores the user's saved locale from `StateStorageService`.
- Missing keys render as `translation-not-found[key]` (`app/config/translation.config.ts`).
- Templates use the `jhiTranslate` directive (`app/shared/language/translate.directive.ts`).
- Supported languages are defined in `app/config/language.constants.ts`. Only English (`en`) is generated, per `.yo-rc.json`. Source bundles live in `src/main/webapp/i18n/en/*.json`, and server-side messages for emails live in `src/main/resources/i18n/messages*.properties`.
- `app/app-page-title-strategy.ts` translates route titles and `pageTitle` keys.

## Routing and Route Resolvers

Every route in `app/app.routes.ts` is lazy-loaded:

| Path                                 | Target                                                                           |
| ------------------------------------ | -------------------------------------------------------------------------------- |
| `''`                                 | `home/home`                                                                      |
| `admin`                              | `admin/admin.routes` (guarded: `UserRouteAccessService`, `authorities: [ADMIN]`) |
| `account`                            | `account/account.route`                                                          |
| `login`                              | `login/login`                                                                    |
| `''`                                 | `entities/entity.routes` (`authority`, `bank-account`, `label`, `operation`)     |
| `error`, `accessdenied`, `404`, `**` | `layouts/error/error.route.ts`                                                   |

Each entity follows the same route pattern (for example `app/entities/operation/operation.routes.ts`):

| Route      | Component | Resolver                     |
| ---------- | --------- | ---------------------------- |
| `''`       | list      | none (default sort `id,asc`) |
| `:id/view` | detail    | `operationResolve`           |
| `new`      | update    | `operationResolve`           |
| `:id/edit` | update    | `operationResolve`           |

**Guards.** `app/core/auth/user-route-access.service.ts` checks the current account. Unauthenticated users go to `/login`, and the requested URL is stored so they return to it after login. Users without the required `authorities` go to `accessdenied`.

**Resolvers.** Functional resolvers live in `app/entities/<entity>/route/<entity>-routing-resolve.service.ts`. Given an `id` param, a resolver fetches the entity through its service and navigates to `404` if the response is empty. Without an `id` (the `new` route), it resolves to `null`.
