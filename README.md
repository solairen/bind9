# About

This Ansible playbook installs and configures [Bind9](https://bind9.readthedocs.io/en/latest/index.html#) as a local recursive DNS resolver with the following features:

- DNS-over-TLS forwarding to [Quad9](https://www.quad9.net/) (`9.9.9.9`, `149.112.112.112`)
- DNSSEC validation
- Rate limiting
- Structured logging to `/var/log/named/`
- Weekly log rotation via cron

A Docker image is available on [DockerHub](https://hub.docker.com/r/moleszek/bind9).

## Supported OS

- Debian 13
- Ubuntu 24.04
- Raspberry Pi 4/5 (arm64)

## Prerequisites

- Ansible `>=2.15`
- Ansible collections (install before running the playbook):

```bash
ansible-galaxy collection install -r requirements.yml
```

## Usage

### 1. Configure inventory

Edit `ansible/inventory.ini` and set the target host address and SSH credentials:

```ini
[bind9]
192.168.1.10

[bind9:vars]
ansible_ssh_user=user
ansible_ssh_pass=password
# ansible_ssh_private_key_file=~/.ssh/id_rsa
ansible_port=22
```

### 2. Configure Bind9

Edit `ansible/group_vars/bind9/configuration.yml`:

```yml
addresses:
  first: 192.168.1.0
  second: 192.168.2.0
dns1: 9.9.9.9
dns2: 149.112.112.112
```

- `addresses` — IP network prefixes (without `/24`) added to the internal ACL. Only hosts within these networks will be allowed to use the resolver. You can add any number of entries:

```yml
addresses:
  first: 192.168.1.0
  second: 192.168.2.0
  third: 192.168.3.0
  fourth: 192.168.4.0
```

The above produces the following ACL in `named.conf.options`:

```conf
acl internal {
    192.168.1.0/24;
    192.168.2.0/24;
    192.168.3.0/24;
    192.168.4.0/24;
};
```

- `dns1` / `dns2` — upstream DNS forwarders used for DNS-over-TLS (port 853).

### 3. Run the playbook

```bash
cd ansible
ansible-playbook install.yml
```

## Ubuntu

On Ubuntu, `systemd-resolved` binds to port 53 by default, which conflicts with Bind9. Disable it before running the playbook:

```bash
sudo vim /etc/systemd/resolved.conf
```

Uncomment and set:

```ini
DNSStubListener=no
```

Save and restart the service:

```bash
sudo systemctl restart systemd-resolved.service
```

## Docker

The Docker image can be used to run the playbook without installing Ansible locally:

```bash
docker run --rm -it moleszek/bind9
```
