# Deploy and Host Gitea with Railway

[![Deploy on Railway](https://railway.com/button.svg)](https://railway.com/deploy/gitea-28-git-forge?referralCode=20H68q)

Gitea is a lightweight, self-hosted Git service: repositories with pull requests, issues, wikis, code review, a package registry and built-in Actions, in one Go binary. This community template runs Gitea v28.4.0 with PostgreSQL 18, Git over HTTPS and SSH, Git LFS, and an administrator created for you on the first boot.

## About Hosting Gitea

Gitea needs one web service with persistent storage for repositories and a database. This template runs the official Gitea container image (Gitea plus OpenSSH), stores repositories, LFS objects, attachments and SSH host keys on a Railway volume, and keeps users, issues and pull requests in Postgres on the private network. The web installer is skipped: everything is configured from Railway variables, a secret key is generated at deploy, registration is closed, and the first administrator is created from variables once the server is healthy. SSH clone and push work through a Railway TCP proxy, and the clone URLs Gitea shows already contain the proxy's host and port. Measured on Railway: about 0.25 GB of RAM for Gitea and 0.13 GB for Postgres.

## Common Use Cases

- Private Git hosting for a team or a company, with pull requests and code review
- A GitHub or GitLab alternative you control, including mirrors of upstream repositories
- Issue tracking, wikis and project boards next to the code
- A package registry (npm, Maven, PyPI, Cargo, OCI containers and more) for internal artifacts
- Hosting open-source projects on your own domain

## Dependencies for Gitea Hosting

- Gitea v28.4.0 (`gitea/gitea`, official image, pinned by digest)
- PostgreSQL 18 (Railway's `postgres-ssl` image)

### Deployment Dependencies

- Gitea documentation: https://docs.gitea.com/
- Source code and releases: https://github.com/go-gitea/gitea
- Container installation guide: https://docs.gitea.com/installation/install-with-docker
- Configuration cheat sheet: https://docs.gitea.com/administration/config-cheat-sheet

### Implementation Details

| Service | Role | Public |
|---|---|---|
| Gitea | Web UI, API, Git over HTTPS and SSH, LFS; volume at `/data` for repositories, LFS objects, attachments, avatars and SSH host keys | HTTPS domain and a TCP proxy for SSH |
| Postgres | Users, organizations, issues, pull requests and settings; private only, with a volume | no |

**First login**

1. Wait until the Gitea service is healthy. The first deploy builds a thin image on top of the official one and creates the database tables, which takes a few minutes.
2. Open the Gitea service → Variables and copy `GITEA_ADMIN_USERNAME` (default `gitea-admin`) and `GITEA_ADMIN_PASSWORD` (generated).
3. Open the Gitea service's public domain and sign in. The deploy log shows `[railway-admin] Administrator 'gitea-admin' created`.
4. Change the administrator's e-mail address in Settings (the default is a placeholder), and add your SSH key under Settings → SSH / GPG keys.

Invalid values never break the deploy: an unusable or reserved user name (such as `api` or `user`) falls back to `gitea-admin`, and a password shorter than 8 characters creates no administrator until you set a longer one and redeploy (registration stays closed, so nobody else can take the instance). The variables are used only while no administrator exists; after that, manage accounts in Gitea. Forgot the password? Set a new `GITEA_ADMIN_PASSWORD` and `GITEA_ADMIN_PASSWORD_RESET=true`, redeploy, then set it back to `false`.

**Users and registration.** Sign-up is closed (`GITEA__service__DISABLE_REGISTRATION=true`). Create accounts in Site administration → User accounts, or set the variable to `false` to open registration. Public repositories are visible without login; set `GITEA__service__REQUIRE_SIGNIN_VIEW=true` for a fully private instance.

**SSH.** The template adds a Railway TCP proxy to port 22. Clone URLs look like `ssh://git@{proxy-host}:{proxy-port}/{owner}/{repo}.git` and are filled in automatically from `RAILWAY_TCP_PROXY_DOMAIN` and `RAILWAY_TCP_PROXY_PORT`. Don't need SSH? Delete the TCP proxy in the service's Networking settings; HTTPS clone and push keep working.

**Custom domain.** Add it in the Gitea service's Settings → Networking, then set `GITEA__server__DOMAIN` and `GITEA__server__ROOT_URL` to it and redeploy.

**Configuration.** Every Gitea setting can be set as a variable named `GITEA__{section}__{KEY}` (for example `GITEA__repository__DEFAULT_PRIVATE=private`); it is written into `app.ini` at each boot. Keep `GITEA__security__SECRET_KEY` unchanged: it encrypts two-factor secrets and other values in the database.

**E-mail.** Set `GITEA__mailer__ENABLED=true` and the `GITEA__mailer__*` settings for notifications and password resets. Railway allows outbound SMTP only on the Pro plan.

**Gitea Actions.** Actions is enabled in Gitea, but this template has no runner: Gitea's act_runner executes jobs in Docker containers, and Railway services cannot run Docker. Register a runner on a machine or VM you control (Settings → Actions → Runners) to run workflows.

**Resources and plans.** Measured on Railway (Gitea 17.0.2, 2026-11-20): Gitea uses about 0.24 GB of RAM at idle and Postgres about 0.13 GB, so a small instance costs roughly $4-6 a month at Railway's usage prices. Every plan works, Free included; repositories grow the volume, so large or many repositories belong on Hobby or Pro.

**Scaling.** Gitea runs as one replica because repositories live on the service's volume. For more users raise the service's CPU and memory; Postgres can be resized independently.

**Backups.** Repositories are on the Gitea volume and metadata in Postgres; back up both (Railway volume backups, or `gitea dump` from a shell).

**Upgrading Gitea.** The version is pinned in `services/gitea/Dockerfile` (tag and digest). When this template is updated, Railway offers the update. To pick a version yourself, eject the service to your own GitHub repository and change the `FROM` line to another release. Database migrations run automatically on start; read the release notes and back up before a major upgrade.

**License.** Gitea is open-source software under the MIT license. This is a community template, not affiliated with the Gitea project or Gitea Ltd.

### Why Deploy Gitea on Railway?

Railway runs Gitea and its Postgres database in one project with a private network, an HTTPS domain, a TCP proxy for SSH, persistent volumes, logs and usage-based pricing. This template gives you a locked-down, ready-to-use Git forge in a few minutes, with SSH clone URLs that work out of the box and no setup wizard to finish.
