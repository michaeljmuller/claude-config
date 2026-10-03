# Working with me
- Be concise. Lead with the answer; skip preamble and restatement.
- Avoid buzzwords and metaphors.  For example, prefer "companion container" to "sidecar"
  and prefer "critical" to the "load bearing".  If you mean "on another host" say that
  insead of "out-of-band".  Say "your default" rather than "house default", "is better"
  rather than "wins", and "is justified" rather than "earns its place".  The only
  exception to this is industry-standard terminology.
- Prefer inclusive terminology ("main/primary" vs "master", "block/allowlist" vs "black/whitelist"
  EXCEPT when referencing software still using dated terminology.
- Ask one question at a time, not batches.
- You can commit, but never push. Keep commit messages concise. Never add
  "Co-Authored-By: Claude" or "Generated with Claude Code" trailers.
- Never commit secrets. Keep them in gitignored local files (`.env`,
  `settings.local.json`); tracked config stays secret-free.
- Don't use the AskUserQuestion tool.  Just ask the questions one-by-one in plain text.
- When writing documentation, minimize markdown syntax and use plain text
  unless there's a genuine advantage to the additional syntax.
- Avoid use of bold/italic for emphasis.

# Where things run
- Prod: a personal Hetzner host — Ubuntu, amd64, rootless Podman. Scope is
  personal/multi-device and friends-and-family, never a wide audience.
- Dev: Mac, arm64, Docker Desktop.
- Only ports 22/80/443 are open (host + network firewall). A containerized Caddy is
  the single front door: it terminates TLS and routes name-based vhosts to apps.
  Apps serve plain HTTP on a host port behind it — TLS is never the app's job.

# Building & running
- Develop entirely in containers. Never install language runtimes, SDKs, or package
  managers on the Mac — build, run, and test in containers. The host keeps only an
  editor, git, and the container runtime.
- Images are built on the server (native amd64) via git pull + Podman: no registry,
  no cross-arch push. Keep `build:` stanzas in compose. Everything must work on both
  podman/amd64 (prod) and docker/arm64 (dev).
- Your job is packaging, not deployment. Deployment, Caddy/DNS edits, server secrets,
  backups, and reboot/autostart all happen on the server and are handled by a release manager.
  Deliver a correctly-packaged app and stop there — no deploy scripts.

# Defaults

These are just defaults; override any of these when there's a clear benefit.

- Storage: choose what suits the task. For a relational database, use PostgreSQL for
  consistency with other projects when nothing else has an advantage; when another
  database (SQLite, for example) is a better fit, use it. Hetzner's S3-compatible
  object storage is available
  when object storage is the best fit (provision a new bucket per need). Local files
  in a volume or bind mount are sometimes the simplest and best solution.
- Persistent state: bind mounts are fine in general; the one real problem under rootless
  Podman is the Postgres image's uid/gid, where a bind-mounted data dir needs an
  out-of-band chown — so use a named volume for Postgres data (and any image with the
  same uid/gid sensitivity).
- Auth: Google OAuth by default; some apps are public or partly public — confirm the
  audience before assuming a gate.
- Git: trunk-based; branch only when working as a team (or simulating one with
  multiple agents).

# New projects
- When starting or packaging a project, use the `new-project` skill for the standard
  layout and packaging conventions. Defer to conventions a toolchain imposes (Maven
  for Java, Xcode for iOS/macOS).
