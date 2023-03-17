# 2023-03-17

## This playbook has been absorbed into the MASH playbook

To avoid maintaining too many similarly-looking playbooks, I've decided to merge this playbook into the newly-created [MASH playbook](https://github.com/mother-of-all-self-hosting/mash-playbook). For more details about this, see the [Why create such a mega playbook?](https://github.com/mother-of-all-self-hosting/mash-playbook/tree/main#why-create-such-a-mega-playbook) section of the new playbook's README file.

**This `peertube-docker-ansible-deploy` playbook will not receive additional updates**.

Steps to migrate from `peertube-docker-ansible-deploy` to hosting PeerTube using the MASH playbook:

1. Get started with the [MASH playbook](https://github.com/mother-of-all-self-hosting/mash-playbook). Do an initial installation which contains Postgres, Redis, Traefik, etc. Enabling the PeerTube service is done below in step 2.

2. Configure the [PeerTube service](https://github.com/mother-of-all-self-hosting/mash-playbook/blob/main/docs/services/peertube.md) with the MASH playbook. You can reuse your `vars.yml` file with these changes:

- the **addition** of `peertube_enabled: true`
- the **removal** of various `peertube_playbook_` variables. You may need to replace them with `mosh_playbook_` variables
- the **removal** of `peertube_generic_secret_key`. This has been superseded by `mash_playbook_generic_secret_key`
- the **removal** of any other variables you had in your old `vars.yml` file (`devture_postgres_connection_password`, etc.). Your new `vars.yml` file likely already defines some of these variables, so there's no need to define them twice.

3. Do an initial installation by running the following command **in the MASH playbook's directory**: `just run-tags install-all`. NOTE: there's a difference between `just run-tags install-all` and `just install-all`; we use the former here, because we don't want to start the PeerTube service just yet

4. SSH into the server and do the following:

   - Create a database dump by running: `/peertube/postgres/bin/dump-all /peertube`. This will create the `/peertube/latest-dump.sql.gz` file

    - Stop and disable all old PeerTube services by running: `cd /etc/systemd/system && systemctl disable --now peertube*` (note the `*` at the end)

    - Sync the PeerTube data by running: `rsync -avr /peertube/peertube/. /mash/peertube/.`

    - Fix permissions for the data: `chown -R mash:mash /mash/peertube`

5. Import the PeerTube database dump into the Postgres instance by running the following command **in the MASH playbook's directory**: `just run-tags import-postgres --extra-vars=server_path_postgres_dump=/peertube/latest-dump.sql.gz --extra-vars=postgres_default_import_database=peertube`

6.  Re-run the MASH playbook and start all services by running the following command **in the MASH playbook's directory**: `just run-tags install-all,start`

7. Verify that your new PeerTube installation works

8. Clean up by SSH-ing into the server and doing the following:

    - `rm /etc/systemd/system/peertube*`
    - `rm -rf /peertube`
    - getting rid of this playbook


# 2023-02-20

Initial release
