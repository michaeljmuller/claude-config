---
name: new-project
description: Use when starting a new project or packaging an app to run on the personal Hetzner/Podman host — scaffolds the standard source layout, the Dockerfile/compose, and the plain-HTTP-behind-Caddy host-port wiring, and assigns a free port. Triggers on "new project", "scaffold", "package this app", "get this ready to deploy/host".
---

# new-project

Scaffold and package a project to run on the personal Hetzner host (Ubuntu, amd64,
rootless Podman), fronted by a containerized Caddy that terminates TLS. Development
happens on a Mac (arm64) under Docker Desktop, so everything must run identically on
both runtimes and both architectures. Images are built on the server via `git pull`;
there is no registry.

All conventions below are **defaults** — override any of them when there's a clear,
stated benefit.

## 1. Standard layout

`<project>`, `<language>`, and `<component>` are placeholders — substitute the real
project and component names when scaffolding.

```
<project>/
  src/
    <language>/<component>/
      <component>/        # importable source package (same name as the component)
        __init__.py
        __main__.py       # entry point, for a runnable/CLI component
      tests/              # test_*.py, beside the component it tests
      pyproject.toml      # per-component; dist name project-prefixed, bare import name
      README.md
    docker/               # ALL deployment artifacts, centralized here
      docker-compose.yml  # single shared compose for every service
      .env                # ${VAR} config/secrets (gitignored)
      <component>/Dockerfile   # build context = src/<language>/<component>
    sql/                  # migrations, mounted read-only into containers
  data/                   # runtime state, bind-mounted (gitignored)
  docs/                   # plain-text design notes
  util/                   # helper scripts
  .gitignore
```

- Organize by language, then component; each component is independently buildable.
- Deployment artifacts never sit beside source or at the repo root — always under
  `src/docker/`.
- When a toolchain imposes its own structure, follow that instead (Maven's
  `src/main/java`, Xcode's project layout, etc.).

## 2. Packaging conventions

- **Dockerfile:** slim base image; copy the component's manifest and package; install
  it; `EXPOSE` the port; `CMD` the server. Build context is the component directory.
- **Compose:** use `build:` stanzas (images are built on the server). Each app binds
  `0.0.0.0` and publishes a host port — privacy comes from the firewall (only
  22/80/443 open), not from binding loopback, because Caddy reaches the app over the
  Podman gateway. Use `profiles:` for on-demand tools run via `compose run --rm`. Add
  healthchecks and `depends_on: { condition: service_healthy }`. Write comments that
  explain *why*.
- **Volumes:** bind mounts are fine for most data (e.g. read-only asset or config
  mounts). Reach for a named volume only where the image's uid/gid makes a bind mount
  painful under rootless Podman — notably the Postgres data directory, whose uid 999
  would otherwise need an out-of-band `podman unshare chown`. Don't blanket-avoid bind
  mounts.
- Must run identically on podman/amd64 (prod) and docker/arm64 (dev). Favor multi-arch
  base images; don't pin an architecture.

## 3. Exposure / Caddy contract (packaging side only)

- The app serves **plain HTTP** on a claimed host port. Caddy adds TLS and the
  name-based vhost, reaching the app at `host.containers.internal:<port>`. TLS is never
  the app's job.
- Caddy config and DNS live on the server and are the user's to change. The packaging
  deliverable just surfaces the wiring for them to apply:
  - the claimed host port,
  - the exact Caddy block to paste:
    `<subdomain>.themullers.org { reverse_proxy host.containers.internal:<port> }`,
  - the subdomain that needs a DNS record.

## 4. Port registry

Before publishing an app, read `ports.md` (next to this file), claim a free host port,
and record the new `subdomain -> port` there. This avoids collisions with apps already
running.

## 5. Data defaults

When in need of a relational db, object store, or authentication provider, consider these
first, but feel free to use something else if there's a clear advantage.

- **Relational DB:** PostgreSQL.
- **Object storage:** the Hetzner S3-compatible store — provision a new bucket per
  need (via boto3 or any S3 SDK) rather than inventing local file storage.
- **Auth:** Google OAuth (OIDC) for anything gated; confirm the audience first, since
  some apps are public or have public sections.
