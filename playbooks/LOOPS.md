# Loops in Ansible

Loops let you repeat a task for multiple items. Use the modern `loop:` keyword (preferred) rather than older `with_items` helpers.

1) Basic list loop (install multiple packages)

```yaml
- name: Install multiple packages
  hosts: servers
  become: true
  vars:
    packages:
      - git
      - htop
      - tree
  tasks:
    - name: Install package
      apt:
        name: "{{ item }}"
        state: present
      loop: "{{ packages }}"
      register: pkg_results

    - name: Show package install results
      debug:
        var: pkg_results.results
```

2) Looping over dictionaries (structured items)

```yaml
- name: Create users
  hosts: servers
  become: true
  vars:
    users:
      - name: alice
        uid: 1001
      - name: bob
        uid: 1002
  tasks:
    - name: Ensure user exists
      user:
        name: "{{ item.name }}"
        uid: "{{ item.uid }}"
        state: present
      loop: "{{ users }}"
      loop_control:
        label: "{{ item.name }}"
```

3) `loop_control` options

- `index_var`: capture loop index
- `label`: human-friendly label used in task output
- `loop_var`: change the loop variable name if you need to keep `item` for another purpose

Example:

```yaml
- name: Example with index
  tasks:
    - debug:
        msg: "#{{ idx }}: installing {{ pkg }}"
      loop: "{{ packages }}"
      loop_control:
        index_var: idx
        loop_var: pkg
```

4) Register and examine results

- When you `register` a task that uses `loop`, the returned object contains `results` — one entry per iteration with `rc`, `stdout`, `changed`, etc.

5) Conditional loops and `when`

- Use `when` on a task together with `loop` to skip iterations based on the item.

```yaml
- name: Install conditional packages
  apt:
    name: "{{ item.name }}"
    state: present
  loop:
    - { name: 'httpd', osfamily: 'RedHat' }
    - { name: 'apache2', osfamily: 'Debian' }
  when: ansible_os_family == item.osfamily
```

6) Common helpers

- `subelements`, `product`, `cartesian` and other filters can be used with `loop` and the `query` lookup plugin to generate combinations.
- `with_items` is deprecated; prefer `loop`.

7) Idempotence & best practices

- Prefer module parameters over `shell`/`command` loops.
- Keep loops small and readable; move complex logic into roles or filters.
- Use `loop_control.label` to make output readable when iterating complex structures.

Example playbook file: `playbooks/multiple_packages.yml`
