# Published apps registry

Claim a free host port and a unique Compose project name before publishing an app,
then record it here. Keep this current as apps come and go — it's the single source
of truth for what's allocated.

- **Host port** must be free on the target host.
- **Compose project name** must be globally unique across the host (set via a
  top-level `name:` in the compose file) — apps whose `docker-compose.yml` lives in a
  directory named `docker` all default to the same project name otherwise, which
  causes different apps' containers to collide and silently overwrite each other.

| Subdomain                 | Host                      | Host port | Compose project  |
|---------------------------|---------------------------|-----------|------------------|
| test.themullers.org       | euhost1.themullers.org    | 8080      | test             |
| vocab.themullers.org      | euhost1.themullers.org    | 5001      | vibevocab        |
| edit.themullers.org       | euhost1.themullers.org    | 3000      | vibeedit         |
| vibelib.themullers.org    | euhost1.themullers.org    | 8000      | vibelib          |
| conjugate.themullers.org  | euhost1.themullers.org    | 8081      | conjugate        |
| keycloak.themullers.org   | euhost1.themullers.org    | 8082      | identity         |
| ldapadmin.themullers.org  | euhost1.themullers.org    | 8083      | identity         |
| (LDAPS, raw TCP)          | euhost1.themullers.org    | 1636      | identity         |
| vault.themullers.org      | euhost1.themullers.org    | 8084      | vault            |
