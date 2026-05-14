# Ansible Configuration Guide

This file shows how to create an `ansible.cfg` file for a small Ansible setup.

It also explains `hosts.ini`, the inventory file referenced by `ansible.cfg`.

## Why use `ansible.cfg`

An `ansible.cfg` file lets you set defaults for inventory, SSH behavior, privilege escalation, and output formatting so you do not need to repeat them on every command.

## 1. Create the config file

In your project directory, create the file:

```
touch ansible.cfg
nano ansible.cfg
```

## 2. Add a basic configuration

Use a configuration like this:

```
[defaults]
inventory = hosts.ini
host_key_checking = False
remote_user = ec2-user
private_key_file = ~/.ssh/ansible-ec2-key
stdout_callback = yaml

[privilege_escalation]
become = True
become_method = sudo
become_ask_pass = False
```

## 3. What each setting does

- `inventory`: default inventory file used by Ansible.
- `host_key_checking`: disables SSH host key prompts for lab environments.
- `remote_user`: default SSH user for managed nodes.
- `private_key_file`: SSH private key used to connect to EC2 instances.
- `stdout_callback`: makes output easier to read.
- `become`: enables privilege escalation when tasks need root access.

## 4. Understand the `hosts.ini` inventory file

`hosts.ini` tells Ansible which machines to manage and how to connect to them.

In your current setup, the inventory groups the two worker nodes under a `servers` group and uses the Ubuntu login user with a private key from the control node.

Current example:

```
[servers]
worker-node-1 ansible_host=35.154.19.150 ansible_user=ubuntu ansible_ssh_private_key_file=/home/ubuntu/ansible-master-key
worker-node-2 ansible_host=3.110.217.103 ansible_user=ubuntu ansible_ssh_private_key_file=/home/ubuntu/ansible-master-key

[all:vars]
ansible_python_interpreter=/usr/bin/python3
ansible_ssh_common_args='-o IdentitiesOnly=yes -o StrictHostKeyChecking=no'
ansible_host_keychecking=false
```

Example:

```
[workers]
worker-1 ansible_host=10.0.1.10 ansible_user=ec2-user
worker-2 ansible_host=10.0.1.11 ansible_user=ec2-user

[workers:vars]
ansible_ssh_private_key_file=~/.ssh/ansible-ec2-key
ansible_python_interpreter=/usr/bin/python3
```

What this means:

- `[workers]`: creates a host group named `workers`.
- `worker-1` / `worker-2`: host aliases used in playbooks.
- `ansible_host`: real IP or DNS name of the host.
- `ansible_user`: SSH username Ansible should use.
- `[workers:vars]`: variables shared by all hosts in the `workers` group.

What your current file means:

- `[servers]`: creates a host group named `servers`.
- `worker-node-1` and `worker-node-2`: friendly names you can use in playbooks instead of raw IPs.
- `ansible_host=35.154.19.150` and `ansible_host=3.110.217.103`: the public IP addresses of the EC2 instances.
- `ansible_user=ubuntu`: tells Ansible to SSH in as the Ubuntu default user.
- `ansible_ssh_private_key_file=/home/ubuntu/ansible-master-key`: the SSH private key Ansible should use.
- `[all:vars]`: applies the listed variables to every host in the inventory.
- `ansible_python_interpreter=/usr/bin/python3`: tells Ansible which Python binary to use on the remote host.
- `ansible_ssh_common_args='-o IdentitiesOnly=yes -o StrictHostKeyChecking=no'`: passes extra SSH options so SSH uses only the specified key and does not stop for host-key prompts in a lab environment.

Important note:

- `ansible_host_keychecking=false` is not the usual Ansible setting name. For host key checking, use `host_key_checking = False` in `ansible.cfg`, or rely on the `ansible_ssh_common_args` SSH option above for a lab-only setup.

Create the file in your project root:

```
nano hosts.ini
```

Then run a connection test:

```
ansible -i hosts.ini workers -m ping
```

## 5. Check that Ansible is using the file

Run:

```
ansible --version
```

The output should show the active configuration file path.

## 6. Example project layout

```
ansible_practice/
├── ansible.cfg
├── hosts.ini
├── playbook.yml
├── ANSIBLE_NOTES.md
└── INSTALLATION.md
```

## 7. Quick test

```
ansible -m ping all
```

If the config file is correct, Ansible should read the inventory and SSH key settings automatically.