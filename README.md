# PeerTube server setup using Ansible and Docker

This [Ansible](https://www.ansible.com/) playbook can help you set up your own [PeerTube](https://joinpeertube.org/) server instance.

PeerTube is is a tool for sharing online videos developed by [Framasoft](https://framasoft.org/), a french non-profit.

This Ansible playbook can set up PeerTube:

- on your own Debian/CentOS/RedHat server

- with all services ([PeerTube](https://joinpeertube.org/), [PostgreSQL](https://www.postgresql.org/), [Traefik](https://traefik.io), etc.) running in [Docker](https://www.docker.com/) containers

- powered by the official [chocobozzz/peertube](https://hub.docker.com/r/chocobozzz/peertube) container image

- [interoperates nicely](docs/configuring-playbook-interoperability.md) with [related](#related) Ansible playbooks or other services using Traefik for reverse-proxying

SSL certificates are automatically managed by a [Traefik](https://traefik.io) reverse-proxy.

Various components (Postgres, Traefik, etc.) can be disabled and replaced with your own other implementations (see [configuring the playbook](docs/configuring-playbook.md)).


## Features

Using this playbook, you can get the following services configured on your server:

- a [PeerTube](https://joinpeertube.org/) server - storing and [transcoding](https://en.wikipedia.org//wiki/Transcoding) your videos

- (optional) a [PostgreSQL](https://www.postgresql.org/) database - required by PeerTube

- (optional) a [Redis](https://redis.io) in-memory datastore - required by PeerTube

- (optional) a [Traefik](https://traefik.io) reverse-proxy server, which obtains free [Let's Encrypt](https://letsencrypt.org/) SSL certificates

Basically, this playbook aims to get you up-and-running with all the basic necessities around PeerTube.


## Installation

To configure and install PeerTube on your own server, follow the [README in the docs/ directory](docs/README.md).


## Changes

This playbook evolves over time, sometimes with backward-incompatible changes.

When updating the playbook, refer to [the changelog](CHANGELOG.md) to catch up with what's new.


## Support

- Matrix room: [#peertube-docker-ansible-deploy:devture.com](https://matrix.to/#/#peertube-docker-ansible-deploy:devture.com)

- GitHub issues: [spantaleev/peertube-docker-ansible-deploy/issues](https://github.com/spantaleev/peertube-docker-ansible-deploy/issues)


## Related

You may also be interested in these other playbooks:

- [matrix-docker-ansible-deploy](https://github.com/spantaleev/matrix-docker-ansible-deploy) - for deploying a fully-featured [Matrix](https://matrix.org) homeserver

- [nextcloud-docker-ansible-deploy](https://github.com/spantaleev/nextcloud-docker-ansible-deploy) - for deploying a [Nextcloud](https://nextcloud.com/) server

- [gitea-docker-ansible-deploy](https://github.com/spantaleev/gitea-docker-ansible-deploy) - for deploying a [Gitea](https://gitea.io) (self-hosted [Git](https://git-scm.com/) service) server

- [vaultwarden-docker-ansible-deploy](https://github.com/spantaleev/vaultwarden-docker-ansible-deploy) - for deploying a [Vaultwarden](https://github.com/dani-garcia/vaultwarden) password manager server (unofficial [Bitwarden](https://bitwarden.com/) compatible server)
