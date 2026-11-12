# vps-baseline

Ansible role meant to take a fresh Debian 12 / Ubuntu 24.04 server to a basic secure baseline:

- non-root admin user with SSH key, password login disabled
- sshd hardened (no root login, key only)
- nftables firewall: SSH, HTTP, HTTPS only (configurable)
- unattended security upgrades
- fail2ban for sshd
- time sync, journald size limit
- Docker CE + compose plugin (optional)

## Status

Study project. The first version of this role was written by Nils Rönnbäck with AI assistance, as learning material for Mae. It has not been run end to end yet and is not used on any production or client server.

It is being worked through step by step in the labs in [til/LABS.md](https://github.com/maenobleza/til/blob/main/LABS.md). Results go under "Tested on" below, and fixes come in as pull requests.

## Usage

Run it only against a disposable test machine. The role turns off password and root SSH login, so `admin_ssh_keys` must contain your public key. The play stops with an error if the list is empty.

```bash
pip install ansible
ansible-galaxy collection install community.general ansible.posix
cp inventory.example.ini inventory.ini   # add your test host
ansible-playbook -i inventory.ini site.yml --check --diff   # dry run first
ansible-playbook -i inventory.ini site.yml
```

First run as `root` (`ansible_user=root` in the inventory). After that, SSH is key-only on `admin_user`; switch `ansible_user` to that.

## Variables

See `roles/baseline/defaults/main.yml`.

| variable | default | purpose |
|---|---|---|
| `admin_user` | `mae` | admin account created on the host |
| `admin_ssh_keys` | `[]` | public keys to authorise (required) |
| `ssh_port` | `22` | sshd listen port |
| `open_tcp_ports` | `[22, 80, 443]` | nftables allow list |
| `install_docker` | `true` | install Docker CE and compose plugin |

## Tested on

Nothing yet. Add one line per run: date, VM type or provider, image, result.

## Known and suspected issues

None of these has been confirmed on a real machine. Each one is a lab task.

- [ ] `Limit journald size` writes to `/etc/systemd/journald.conf.d/` without creating the directory first.
- [ ] The Docker apt repository line hardcodes `arch=amd64`, so it will fail on arm64 machines.
- [ ] The nftables template sets the forward chain to `policy drop` and starts with `flush ruleset`. Both are likely to break Docker networking and published ports when `install_docker` is true.
- [ ] On Ubuntu 24.04, sshd is socket-activated. Changing `ssh_port` in `sshd_config.d` alone may not change the listening port.
- [ ] No automated tests (Molecule or CI).

License: MIT
