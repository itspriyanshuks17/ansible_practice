# Ansible Installation Guide

This document shows recommended ways to install Ansible on major operating systems and environments. It covers distribution packages, Python/pip, Homebrew, and using WSL on Windows. Choose the method that best fits your needs (stability vs. latest features).

## Quick verification

After installing, verify with:

```
ansible --version
```

You can run a quick local check:

```
# create an inventory file with 'localhost'
echo "localhost ansible_connection=local" > inventory
ansible -m ping all -i inventory
```

## Installation methods overview

- Distribution package (apt/dnf/yum/pkg): easiest, stable version from OS vendor.
- pip / virtualenv / pipx: best for latest versions or isolated installs.
- Homebrew (macOS): simple and well-integrated for macOS users.
- WSL (Windows): recommended approach for running Ansible on Windows.

---

## Linux

General prerequisites:

- Python 3 (system Python) and `pip` for Python-based installs.
- `sudo` or root access for system package installs.

### Debian / Ubuntu

Option A — OS packages (stable):

```
sudo apt update
sudo apt install -y ansible
```

Option B — Latest via pip (isolated):

```
python3 -m pip install --user virtualenv
python3 -m virtualenv ~/.venv/ansible
source ~/.venv/ansible/bin/activate
pip install --upgrade pip
pip install ansible
```

Tip: Use `pip install ansible-core` if you only need the core runtime and plan to manage collections separately.

### RHEL / CentOS / AlmaLinux / Rocky

Option A — EPEL (distro package):

```
sudo yum install -y epel-release
sudo yum install -y ansible
```

Option B — Fedora (dnf):

```
sudo dnf install -y ansible
```

Option C — pip (isolated virtualenv): same steps as Debian pip option above.

### Amazon Linux

```
# Amazon Linux 2
sudo amazon-linux-extras enable ansible2
sudo yum clean metadata
sudo yum install -y ansible
```

### Containers

Running Ansible from a container is convenient for CI or ephemeral control nodes. Example (community image):

```
docker run --rm -it -v $(pwd):/work -w /work williamyeh/ansible:alpine3 ansible --version
```

---

## macOS

Option A — Homebrew (recommended):

```
brew update
brew install ansible
```

Option B — pip / virtualenv:

```
python3 -m pip install --user virtualenv
python3 -m virtualenv ~/.venv/ansible
source ~/.venv/ansible/bin/activate
pip install ansible
```

---

## Windows

Ansible is not supported as a control node on native Windows. Recommended approaches:

1. Use WSL (Windows Subsystem for Linux) and install Ansible in the Linux distribution (Ubuntu recommended):

```
# From PowerShell (as Admin) to enable WSL and install Ubuntu
wsl --install -d Ubuntu
# Then open Ubuntu and follow the Linux instructions above
```

2. Use a Linux VM or a remote control node.

Notes: Windows machines can still be managed by Ansible as managed nodes using WinRM; the Ansible control node should be Linux/macOS/WSL.

---

## FreeBSD (brief)

```
sudo pkg install py39-ansible
```

Package names and Python versions may vary; prefer the `pkg` repository or a Python virtualenv if needed.

---

## Installing via pipx (recommended for single-user isolated installs)

If you use `pipx` you can install Ansible in an isolated, upgradeable environment that puts the CLI on your PATH:

```
python3 -m pip install --user pipx
python3 -m pipx ensurepath
pipx install ansible
```

If `ansible` isn't available as a pipx package in your environment, use `pipx install --pip-args='...' ansible-core` and expose the desired entrypoints instead.

---

## Post-install verification & quick example

1. Check version and paths:

```
ansible --version
which ansible
```

2. Quick ping local host:

```
echo "[local]\nlocalhost ansible_connection=local" > hosts
ansible -m ping all -i hosts
```

3. Minimal playbook (save as `playbook.yml`):

```
- hosts: localhost
	connection: local
	gather_facts: no
	tasks:
		- debug: msg="Ansible is working"
```

Run it:

```
ansible-playbook -i hosts playbook.yml
```

---

## Troubleshooting & tips

- Use virtualenv or pipx to avoid conflicting distro packages and to get newer versions.
- On RHEL-family systems, ensure EPEL is enabled for the distro-provided `ansible` package.
- If `ansible` CLI isn't found after pip install, ensure `~/.local/bin` is on your PATH or activate your virtualenv.
- For Windows users, prefer WSL to run Ansible; managing Windows hosts requires WinRM configuration on the target.

---

## Further reading

- Official docs: https://docs.ansible.com/
- Ansible Collections & Galaxy: https://galaxy.ansible.com/

- Official distro-specific install instructions: https://docs.ansible.com/projects/ansible/latest/installation_guide/installation_distros.html

If you'd like, I can expand any section (examples for RHEL provisioning, WinRM setup, or a Docker-based control node). 
