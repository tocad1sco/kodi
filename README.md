# DriveMe IaC

# Requirements
- Python + Virtualenv
- Ansible .vault_pass file should be presented in root directory

# Installation

```bash
$ source /path/to/your/venv/bin/activate
$ git clone git@gitlab.com:imhio/driveme_iac.git
$ cd driveme-iac/ansible
# Install Python requirements if needed
$ pip install -r ./requirements.txt
# Install roles or add new roles (not update)
$ ansible-galaxy install -r ./requirements.yml
# Install roles or add new collections (not update)
$ ansible-galaxy collection install -r ./requirements.yml
```

# Update installed roles

Unfortunaly Ansible can't update installed roles. Fastest way to update installed roles is:

```bash
$ rm -rf roles/*
$ ansible-galaxy install -r ./requirements.yml
$ ansible-galaxy collection install -r ./requirements.yml
```

# Add new role

To add new role - edit `requirements.yml` file and push your changes.
It's good to keep alphabetical order of required roles.

# Playbook folders

- `bootstrap` contains playbooks used to perform initial setup for new servers
- `operations` contains playbooks used to perform common management tasks
- `applications` contains main project playbooks related to specified host group

# New server setup

```bash
# Allow Nginx access for all project servers
ansible-playbook --forks 3 -i inventory/inventory.yml playbooks/bootstrap/00-nginx-repo-access.yml --key-file=/path/to/ssh/key

# Run base-config with limit on host/group
$ ansible-playbook --forks 3 -i inventory/inventory.yml playbooks/bootstrap/01-base-config.yml --key-file=/path/to/ssh/key --limit <new_hosts> -e '{"kernel_management_reboot": true,"base_config_upgrade_all": true}' 

# Run your main application playbook
$ ansible-playbook --forks 3 -i inventory/inventory.yml playbooks/applications/<playbook_name>/main.yml --key-file=/path/to/ssh/key --limit <server_group> --diff 
```

# Management playbooks

You can run them for performing common management tasks with or without limit.

```bash
# Configure iptables
$ ansible-playbook -i inventory/production playbooks/operations/iptables.yml -u ansible -b --key-file=/path/to/ssh/key
# Update kernel
$ ansible-playbook -i inventory/production playbooks/operations/kernel.yml -u ansible -b --key-file=/path/to/ssh/key --limit <group> -e '{"kernel_management_reboot": true}'
```
