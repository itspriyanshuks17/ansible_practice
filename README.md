# Ansible Practice

This repository contains notes and guides for learning and using Ansible.

- Installation guide: [INSTALLATION.md](INSTALLATION.md)
- Ansible config + inventory guide: [ANSIBLE_CONFIG.md](ANSIBLE_CONFIG.md)
- Core notes, EC2 architecture, and lab flow: [ANSIBLE_NOTES.md](ANSIBLE_NOTES.md)
- Sample inventory file: [hosts.ini](hosts.ini)

## Learning path (in order)

1. Install Ansible using [INSTALLATION.md](INSTALLATION.md).
2. Read the basics in [ANSIBLE_NOTES.md](ANSIBLE_NOTES.md): what Ansible is, why it is used, and key concepts.
3. Set up your AWS lab architecture (1 control node + 2 workers) from [ANSIBLE_NOTES.md](ANSIBLE_NOTES.md), including `ssh-keygen` and SSH verification.
4. Create `ansible.cfg` and understand each setting from [ANSIBLE_CONFIG.md](ANSIBLE_CONFIG.md).
5. Create and update your inventory in [hosts.ini](hosts.ini) using your worker private IPs.
6. Run connectivity checks:

```
ansible -i hosts.ini workers -m ping
```

7. Run your first playbook from the examples in [ANSIBLE_NOTES.md](ANSIBLE_NOTES.md).

## Suggested first milestone

When this command succeeds, your setup is ready for playbooks:

```
ansible -m ping all
```

Contributing

Feel free to add more topics to `ANSIBLE_NOTES.md` — send topics and I'll draft concise notes and diagrams.
