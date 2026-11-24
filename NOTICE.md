# Notice

## Upstream software

This repository contains Railway configuration and a small bootstrap script only. Gitea is pulled at build time from its official container image; it is not stored in this repository.

| Software | License | Source (publisher) |
|---|---|---|
| Gitea v28.4.0 (with OpenSSH, s6 and the Alpine base the image ships) | MIT (Gitea); the image's Alpine packages under their own licenses | `gitea/gitea:28.4.0@sha256:aa90e8f00b755b0d665214ab9c14447ea44d3190dbe6b6d0cb46cd9c4f55d309` (Gitea project, Docker Hub) |
| PostgreSQL 18 | PostgreSQL License | `ghcr.io/railwayapp-templates/postgres-ssl:18.6@sha256:4fc833b43d2aaf6ecc9b18615f5533602b723a32d0e2efe2dae8ca0f7217c863` (Railway; MIT-licensed image repository) |

Gitea's license: https://github.com/go-gitea/gitea/blob/v28.4.0/LICENSE. Source code for the pinned release: https://github.com/go-gitea/gitea/tree/v28.4.0.

## Verified against

| What | Upstream file | Ref | Checked |
|---|---|---|---|
| Latest stable release | GitHub releases (`v28.4.0`, 2026-11-15) and Docker Hub tags (`28.4.0` = `28` = `latest`) | `v28.4.0` | 2026-11-24 |
| Image layout, entrypoint, s6 services (gitea, openssh), first-boot `app.ini` template | `Dockerfile`, `docker/root/usr/bin/entrypoint`, `docker/root/etc/s6/*/run` and `setup`, `docker/root/etc/templates/app.ini`, `docker/root/usr/local/bin/gitea` | `v28.4.0` | 2026-11-24 |
| `GITEA__section__KEY` variables written to `app.ini` | `docker/root/usr/local/bin/environment-to-ini` | `v28.4.0` | 2026-11-24 |
| `gitea admin user create/list/change-password` flags | `cmd/admin_user_create.go`, `cmd/admin_user_list.go`, `cmd/admin_user_change_password.go` | `v28.4.0` | 2026-11-24 |
| Reserved user names | `models/user/user.go` (`reservedUsernames`) | `v28.4.0` | 2026-11-24 |
| Health endpoint `/api/healthz` | `routers/web/healthcheck/check.go` | `v28.4.0` | 2026-11-24 |
| Railway Postgres image (`18.6` = `18`) | ghcr.io tags of `railwayapp-templates/postgres-ssl` | 18.6 | 2026-11-20 |

## Adapted code

| Our file | Source | Changes |
|---|---|---|
| `services/gitea/railway-admin` | Written for our Forgejo Railway template (same author); follows the s6 service layout of Gitea's image | Gitea names, Gitea's reserved user names |

## Trademarks

"Gitea", "Railway" and "PostgreSQL" belong to their respective owners. This is a community template and is not affiliated with or endorsed by them.
