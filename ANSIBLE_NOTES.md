# Ansible Notes — Why, What, When

## What is Ansible?

- Ansible is an ***open-source IT automation***, configuration management, and orchestration tool maintained by Red Hat and the community.
- It uses an ***agentless architecture***: the control node connects to managed nodes over **SSH (Linux/Unix)** or **WinRM (Windows)**.
- ***Playbooks*** are YAML files that declare desired state using tasks and modules. Collections provide reusable modules/roles.

## Why use Ansible?

- Simple and human-readable: YAML playbooks are easy to write and review.
- Agentless: no persistent agents to manage on target hosts (reduces footprint).
- Idempotent: modules try to apply changes only when needed, enabling safe repeatable runs.
- Extensible: many built-in modules plus Collections and Galaxy for community content.
- Wide ecosystem: works with cloud providers, network devices, containers, and Windows.
- Good for both ad-hoc commands and full lifecycle automation (config, deploy, orchestration).

## When to use Ansible?

- Provisioning infrastructure and configuring servers after provisioning.
- Deploying applications, especially where declarative configuration fits.
- Automating routine tasks across many machines (patching, user management, packages).
- Orchestrating multi-step workflows (deploy DB → migrate → deploy app → smoke tests).
- Managing network devices where supported modules exist.
- When you prefer agentless, SSH-based automation and need quick onboarding for engineers.

## When not to use Ansible (limitations)

- Real-time, low-latency orchestration—Ansible is not optimized for extremely low-latency control loops.
- Very large-scale event-driven automation—consider event-driven systems or automation platforms for highly dynamic topologies.
- Where a persistent agent with push/pull features is required (use Salt, Chef, or Puppet in those cases).

## Key concepts (quick)

- Control node: machine where Ansible runs (can be local workstation, CI, or dedicated control host).
- Managed nodes: target hosts defined in inventory.
- Inventory: list of hosts (static file or dynamic inventory from cloud APIs).
- Playbook: YAML document of plays; a play maps hosts → tasks.
- Module: unit of work (e.g., `yum`, `apt`, `copy`, `template`).
- Role: reusable grouping of tasks, defaults, handlers, files, templates.
- Collection: packaged set of plugins, modules, roles distributed via Galaxy.

## Quick example

Inventory (hosts):

```
[web]
web1.example.com

[db]
db1.example.com
```

Simple playbook:

```
- hosts: web
  become: yes
  tasks:
    - name: Ensure nginx is installed
      apt:
        name: nginx
        state: present

    - name: Start nginx
      service:
        name: nginx
        state: started
```

Run:

```
ansible-playbook -i hosts site.yml
```

## Best practices (brief)

- Use `ansible.cfg` to centralize defaults; keep inventories small and logical groups.
- Pin collection and role versions; prefer `ansible-galaxy` requirements files for reproducible installs.
- Use `become` for privilege escalation; don't SSH as root unless required.
- Keep secrets out of git — use `ansible-vault` for encrypting sensitive data.
- Test playbooks in a disposable environment (containers/VMs) before production runs.

## Where to learn more

- Official docs: https://docs.ansible.com/
- Collections & Galaxy: https://galaxy.ansible.com/

---

## Architecture diagram

Below is a high-level architecture diagram describing a typical Ansible setup: the control node, inventories, managed nodes (Linux and Windows), optional Execution Environments, and integration with cloud provider APIs.

```mermaid
flowchart LR
  subgraph ControlNode[Control node]
    C[Ansible control node<br/>(playbooks, roles, collections)]
  end

  subgraph Inventory[Inventory]
    I[Static / Dynamic inventory]
  end

  subgraph Connections[Protocols]
    SSH[SSH]
    WinRM[WinRM]
  end

  subgraph Managed[Managed hosts]
    L1[Linux host 1]
    L2[Linux host 2]
    W1[Windows host]
  end

  subgraph Cloud[Cloud Providers]
    CloudAPI[Cloud APIs (AWS/Azure/GCP)]
    VM[Provisioned VMs / Instances]
  end

  subgraph ExecEnv[Execution Envs]
    EE[Execution Environments / Containers]
  end

  C --> I
  C --> SSH
  C --> WinRM
  SSH --> L1
  SSH --> L2
  WinRM --> W1
  C --> CloudAPI
  CloudAPI --> VM
  C --> EE
  EE --> VM

  classDef box stroke:#333,stroke-width:1px,fill:#f8f9fa
  class ControlNode,Inventory,Managed,Cloud,ExecEnv box

```

Notes:

- The control node runs `ansible` / `ansible-playbook` and reads the inventory (static file or dynamic script/plugin).
- Connections to Linux targets use SSH; Windows targets use WinRM (requires extra config).
- Execution Environments (containerized runtimes) are optional and used to isolate runtime dependencies.
- Cloud APIs are used for provisioning and dynamic inventory sources.

