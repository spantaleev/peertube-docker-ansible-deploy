# Installing

After [configuring DNS](configuring-dns.md) and [configuring the playbook](configuring-playbook.md), you're ready to install.

**Before installing** and each time you update the playbook in the future, you will need to update the Ansible roles in this playbook by running `just roles`. `just roles` is a shortcut (a `roles` target defined in [`justfile`](../justfile) and executed by the [`just`](https://github.com/casey/just) utility) which ultimately runs [ansible-galaxy](https://docs.ansible.com/ansible/latest/cli/ansible-galaxy.html) to download Ansible roles. If you don't have `just`, you can also manually run the `roles` commands seen in the `justfile`.


## Playbook tags introduction

The Ansible playbook's tasks are tagged, so that certain parts of the Ansible playbook can be run without running all other tasks.

The general command syntax is: `ansible-playbook -i inventory/hosts setup.yml --tags=COMMA_SEPARATED_TAGS_GO_HERE`

Here are some playbook tags that you should be familiar with:

- `setup-all` - runs all setup tasks (installation and uninstallation) for all components, but does not start/restart services

- `install-all` - like `setup-all`, but skips uninstallation tasks. Useful for maintaining your setup quickly when its components remain unchanged. If you adjust your `vars.yml` to remove components, you'd need to run `setup-all` though, or these components will still remain installed

- `setup-SERVICE` (e.g. `setup-peertube`) - runs the setup tasks only for a given role, but does not start/restart services. You can discover these additional tags in each role (`roles/*/main.yml`). Running per-component setup tasks is **not recommended**, as components sometimes depend on each other

- `install-SERVICE` (e.g. `install-peertube`) - like `setup-SERVICE`, but skips uninstallation tasks. See `install-all` above for additional information.

- `start` - starts all systemd services and makes them start automatically in the future

- `stop` - stops all systemd services

`setup-*` tags and `install-*` tags **do not start services** automatically, because you may wish to do things before starting services, such as importing a database dump, restoring data from another server, etc.


## Installation

Run the playbook: `ansible-playbook -i inventory/hosts setup.yml --tags=install-all`.

If your inventory file (`vars.yml`) contains encrypted variables, you may need to pass `--ask-vault-pass` to the `ansible-playbook` command.

After installing, you can start services with `just start-all` (or `ansible-playbook -i inventory/hosts setup.yml --tags=start`).

**Hint**: In the future (especially if you'll be disabling playbook components), you may wish to use the `setup-all` tag (instead of `install-all`) to ensure that disabled components are uninstalled correctly. Read more about [playbook tags](#playbook-tags-introduction) above.


## Initial PeerTube setup

After some time, you should be able to access your new PeerTube instance at: `https://YOUR_PEERTUBE_HOSTNAME` (matching the `peertube_hostname` value you've set in `vars.yml`).

You should then be able to log in with:

- username: `root`
- password: the password you've set in `peertube_config_root_user_initial_password` in `vars.yml`


## Adjusting the trusted reverse-proxy networks

If you go to **Administration** -> **System** -> **Debug** (`/admin/system/debug`), you'll notice that PeerTube reports some local IP instead of your own IP address.

To fix this, you need to adjust the "trusted proxies" configuration setting.

The default installation uses a Traefik reverse-proxy, so we suggest that you make PeerTube trust the whole `traefik` container network.

To do that:

- SSH into the machine
- run this command to find the network range: `docker network inspect traefik -f "{{ (index .IPAM.Config 0).Subnet }}"` (e.g. `172.19.0.0/16`)
- adjust your `vars.yml` configuration to contain a variable like this: `peertube_trusted_proxies_values_custom: ["172.19.0.0/16"]`

Then, re-install the PeerTube component via the playbook -- `just run-tags install-peertube,start`

You should then see the **Debug** page report your actual IP address.
