# Conditions in Ansible (the `when` keyword)

`when` controls whether a task, handler, block, or whole play runs. It accepts a Jinja2 expression (string) or a YAML list (AND semantics).

Basic usage

```yaml
- name: Install htop on Debian-family
  apt:
    name: htop
    state: present
  when: ansible_os_family == 'Debian'
```

Common patterns

- Equality / comparison:
  - `when: ansible_distribution == 'Ubuntu'`
  - `when: ansible_distribution_major_version | int >= 20`
  - `when: ansible_distribution_version is version('20.04', '>=')`

- Boolean / truthy checks:
  - `when: my_var` (true if `my_var` exists and evaluates truthy)
  - `when: my_var | bool` (coerce strings like "true"/"false")

- Defined / undefined:
  - `when: my_var is defined`
  - `when: my_var is not defined`

- Using registered results:

```yaml
- command: /usr/bin/some-check
  register: check

- name: Run only when check succeeded
  debug: msg="OK"
  when: check.rc == 0
```

- Loop item conditions:

```yaml
- name: Install package only on Debian
  apt:
    name: "{{ item }}"
    state: present
  loop: "{{ packages }}"
  when: ansible_os_family == 'Debian'
```

- Condition per-item (use `when` with `item` properties):

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

- Multiple expressions (AND):

```yaml
when:
  - ansible_os_family == 'Debian'
  - ansible_distribution_major_version | int >= 20
```

- Negation / OR:

```yaml
when: not (ansible_os_family == 'RedHat')
when: ansible_distribution == 'Ubuntu' or ansible_distribution == 'Debian'
```

Advanced tips

- Use `stat` and `register` to make decisions about files:

```yaml
- stat:
    path: /etc/some.conf
  register: conf

- name: Create default file when absent
  copy:
    dest: /etc/some.conf
    content: "default"
  when: not conf.stat.exists
```

- Use `failed_when` / `changed_when` to control task outcome and idempotence when necessary.

```yaml
- command: /usr/bin/might-fail
  register: out
  failed_when: out.rc not in [0,2]
```

- Always prefer module checks to shell-based checks for reliability and idempotence.

- Coerce types when needed: use `| int`, `| bool`, `| default()` to avoid undefined errors.

Example playbook: `playbooks/conditions.yml`
