# Ansible Commands Reference

This file explains the most important Ansible CLI commands and when to use them.

Ansible has many plugins and options, so this guide focuses on the commands you will use most often while learning and working with Ansible.

## 1. `ansible`

Use this command for ad-hoc tasks on managed nodes.

Examples:

```
ansible -i hosts.ini servers -m ping
ansible -i hosts.ini servers -m command -a "uptime"
ansible -i hosts.ini servers -m shell -a "df -h"
```

![alt text](images/ansible-uptime.png)

What it does:

- Runs a module on one or more hosts.
- Good for quick checks and one-off tasks.
- Uses inventory groups such as `servers` or `workers`.

Useful options:

- `-i hosts.ini`: use a specific inventory file.
- `-m ping`: run the `ping` module.
- `-a "..."`: pass arguments to the module.
- `-u ubuntu`: override the SSH user.
- `--ask-become-pass`: prompt for sudo password if needed.

## 2. `ansible-playbook`

Use this command to run YAML playbooks.

Examples:

```
ansible-playbook -i hosts.ini playbook.yml
ansible-playbook -i hosts.ini playbook.yml --syntax-check
ansible-playbook -i hosts.ini playbook.yml --check
```

What it does:

- Executes a playbook against the inventory.
- Best for repeatable automation.
- Supports roles, tasks, handlers, and variables.

Useful options:

- `--syntax-check`: validates YAML and playbook structure.
- `--check`: dry run mode.
- `--diff`: shows file/content changes where supported.
- `--limit servers`: run only on a subset of hosts.

## 3. `ansible-inventory`

Use this command to inspect inventory data.

Examples:

```
ansible-inventory -i hosts.ini --list
ansible-inventory -i hosts.ini --graph
```

What it does:

- Shows the parsed inventory structure.
- Helps confirm groups, host aliases, and variables.
- Useful when debugging inventory problems.

## 4. `ansible-config`

Use this command to inspect Ansible configuration settings.

Examples:

```
ansible-config view
ansible-config dump --only-changed
```

What it does:

- Displays active Ansible settings.
- Helps confirm that `ansible.cfg` is being used.
- Shows defaults and overridden values.

## 5. `ansible-doc`

Use this command to read documentation for modules, plugins, and collections.

Examples:

```
ansible-doc ping
ansible-doc copy
ansible-doc -l
```

What it does:

- Shows module parameters, examples, and notes.
- Helps you learn which module to use.

## 6. `ansible-galaxy`

Use this command to manage roles and collections.

Examples:

```
ansible-galaxy collection install community.general
ansible-galaxy role init webserver
```

What it does:

- Installs reusable content from Ansible Galaxy.
- Creates a role skeleton.
- Manages dependencies for roles and collections.

## 7. `ansible-vault`

Use this command to encrypt and manage secrets.

Examples:

```
ansible-vault create secrets.yml
ansible-vault edit secrets.yml
ansible-vault view secrets.yml
ansible-vault encrypt_string 'mypassword' --name 'db_password'
```

What it does:

- Protects sensitive data such as passwords and API keys.
- Keeps secrets out of plain text files in git.

## 8. `ansible-pull`

Use this command when managed nodes pull a playbook from a repository instead of the control node pushing it.

Example:

```
ansible-pull -U https://github.com/example/repo.git site.yml
```

What it does:

- Pulls playbooks from git and runs them locally on the target host.
- Useful for self-updating systems or scheduled automation.

## 9. `ansible-test`

Use this command when developing Ansible content such as modules, plugins, or collections.

Example:

```
ansible-test sanity
```

What it does:

- Runs sanity and integration tests for Ansible content.
- Mostly used by contributors and advanced users.

## 10. Common command workflow

For a new lab or project, this order is usually helpful:

1. Check inventory with `ansible-inventory`.
2. Check settings with `ansible-config`.
3. Read module docs with `ansible-doc`.
4. Test connectivity with `ansible -m ping`.
5. Run your playbook with `ansible-playbook`.
6. Protect secrets with `ansible-vault`.

## 11. Quick examples for your AWS lab

```
ansible -i hosts.ini servers -m ping
ansible-playbook -i hosts.ini playbook.yml
ansible-inventory -i hosts.ini --graph
ansible-config dump --only-changed
```

## 12. Important note

Ansible has many subcommands and plugin-specific options, so this guide covers the core commands you will use most often. If you want, this file can be expanded with a module-by-module reference later.