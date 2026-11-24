# Gitea for Railway: the official rootful image (Gitea + OpenSSH under s6) plus one
# extra s6 service that creates the first administrator from Railway variables.
#
# Pinned by tag and digest. To upgrade, change both to a newer release:
# https://hub.docker.com/r/gitea/gitea/tags (release notes: https://github.com/go-gitea/gitea/releases)
FROM gitea/gitea:28.4.0@sha256:aa90e8f00b755b0d665214ab9c14447ea44d3190dbe6b6d0cb46cd9c4f55d309

# s6-svscan (started by the image's CMD) supervises every directory in /etc/s6:
# gitea (the web server), openssh and our one-shot railway-admin.
COPY --chmod=755 railway-admin /etc/s6/railway-admin/run

# Fail the build early if the tools the bootstrap needs are missing.
RUN /usr/local/bin/gitea --version \
 && command -v su-exec curl s6-svc >/dev/null \
 && bash -n /etc/s6/railway-admin/run
