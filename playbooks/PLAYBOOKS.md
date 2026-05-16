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

## Interpreting playbook output

When you run `ansible-playbook -i ../hosts.ini packages.yml -v` you may see output like the example you provided. Here's how to read it:

- `No config file found; using defaults` — no `ansible.cfg` found in the current directory or parent; Ansible uses its default settings. Add a project `ansible.cfg` to control defaults (inventory, forks, remote_user, etc.).

- `PLAY [Install Packages]` — the play has started; the bracketed text is the play `name:` from your playbook.

- `TASK [Gathering Facts]` — Ansible runs the `setup` module to collect facts about each host. `ok: [host]` means facts were collected successfully.

- `TASK [Install Package]` followed by `changed: [worker-node-1] => { ... }` — the module ran on that host and returned a JSON-like result object. Key fields:
  - `changed: true` indicates the module made a change (e.g., installed a package).
  - `stdout` / `stderr` and their `_lines` variants show command/module output; with `-v` you see the full data structure.
  - `cache_updated: false` / `cache_update_time` are module-specific details (here from the `apt` module) showing whether the APT cache was refreshed.

- The `stderr` warning in your output: `W: --force-yes is deprecated` — this comes from `apt`/`apt-get`. It means the underlying apt option used is deprecated; avoid `force: yes` in `apt` and prefer safe module parameters or the newer apt options (or see distro docs). The task still succeeded.

- The long `stdout` shows apt's download/unpack logs and any post-install checks (e.g., `needrestart` messages). This is normal verbose output shown because you used `-v` (verbose).

- `PLAY RECAP` — a compact summary per host with counters:
  - `ok` = tasks that reported OK
  - `changed` = tasks that made changes
  - `unreachable` = hosts that couldn't be contacted
  - `failed` = tasks that failed
  - `skipped`, `rescued`, `ignored` — other outcomes

### Quick tips

- Use `--list-hosts` to preview which hosts will run: `ansible-playbook -i ../hosts.ini packages.yml --list-hosts`.
- `-v` increases verbosity; `-vvv` or `-vvvv` show progressively more debug output. Use `-v` to see module return data (stdout/stderr) as above.
- Prefer module-native options to avoid deprecated flags. For `apt`, avoid `force: yes` if it triggers `--force-yes`; instead rely on the module's idempotence and consider more specific options only when necessary.
- For less noisy runs remove `-v` to see concise progress and the `PLAY RECAP` summary.

If you want, I can paste an annotated copy of your exact output into this file with inline explanations for each section.
