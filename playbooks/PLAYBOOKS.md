# Playbooks — Concepts and Structure

This file explains core playbook concepts and a minimal example to get started.

What is a playbook?

- A YAML file defining one or more "plays" mapping hosts to tasks.
- Use playbooks for repeatable, idempotent automation (preferred over ad-hoc commands).

Basic structure

```yaml
- name: Short description
  hosts: webservers
  become: true
  vars:
    myvar: value
  tasks:
    - name: Ensure package installed
      apt:
        name: nginx
        state: present
    - name: Start service
      service:
        name: nginx
        state: started
```

Key elements

- `hosts`: target group or host.
- `become`: run tasks with privilege escalation.
- `vars` / `vars_files`: provide variables for the play.
- `tasks`: list of actions using modules (`apt`, `service`, `template`, etc.).
- `handlers`: tasks triggered by `notify:` (e.g., restart a service).
- `roles`: reusable collections for larger projects.
- `tags`: mark tasks to run selectively with `--tags`.

Example: minimal `hello.yml`

```yaml
- name: Hello example
  hosts: all
  tasks:
    - name: Print greeting
      debug:
        msg: "Hello from {{ inventory_hostname }}"
```

Run it:

```bash
ansible-playbook -i ../hosts.ini playbooks/hello.yml
```

Tips

- Prefer modules to shell commands for idempotence.
- Use `--check` for dry runs and `--diff` to see file changes.
- Keep inventories and `ansible.cfg` in the project root for predictable runs.
