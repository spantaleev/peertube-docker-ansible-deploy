# Configuring the database

By default, this playbook installs Postgres in a container alongside PeerTube.

To use your own Postgres server, use a `vars.yml` configuration like this:

```yaml
# Disable the integrated Postgres service
devture_postgres_enabled: false

# Fill these out
peertube_config_database_hostname: ''
peertube_config_database_port: 5432
peertube_config_database_name: peertube
peertube_config_database_username: ''
peertube_config_database_password: ''
```
