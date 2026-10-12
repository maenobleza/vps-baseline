# vps-baseline

Ansible role that takes a fresh Debian 12 / Ubuntu 24.04 VPS to a secure baseline:

- non-root admin user with SSH key, password login disabled
- sshd hardened (no root login, key only)
- nftables firewall: SSH, HTTP, HTTPS only (configurable)
- unattended security upgrades
- fail2ban for sshd
- time sync, journald size limit
- Docker CE + compose plugin (optional)

I use this on every client server before deploying anything else. Idempotent: rerunning it changes nothing on a server that already matches.

## Usage

```bash
pip install ansible
ansible-galaxy collection install community.general ansible.posix
cp inventory.example.ini inventory.ini   # add your host
ansible-playbook -i inventory.ini site.yml
```

First run as `root` (`ansible_user=root` in the inventory). After that, SSH is key-only on `admin_user`; switch `ansible_user` to that.

## Variables

See `roles/baseline/defaults/main.yml`.

| variable | default | purpose |
|---|---|---|
| `admin_user` | `mae` | admin account created on the host |
| `admin_ssh_keys` | `[]` | public keys to authorise |
| `ssh_port` | `22` | sshd listen port |
| `open_tcp_ports` | `[22, 80, 443]` | nftables allow list |
| `install_docker` | `true` | install Docker CE and compose plugin |

## Tested on

(fill in after first real run: provider, image, date)

## Roadmap

- [ ] Molecule tests in CI
- [ ] CrowdSec as an alternative to fail2ban
- [ ] restic backup role

License: MIT
