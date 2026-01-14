# About

This ansible script installs [Bind9](https://bind9.readthedocs.io/en/latest/index.html#).

Docker image is available on [DockerHub](https://hub.docker.com/r/moleszek/bind9).

Supported OS:

- Debian 12
- Ubuntu 22.04
- Raspberry Pi 4/5

## Ubuntu

In Ubuntu OS `DNSStubListener` should be set to `no`:

```bash
vim /etc/systemd/resolved.conf
```

uncomment and change to `no`:

```bash
DNSStubListener=no
```

Save and restart `systemd-resolved.service`

```bash
sudo restart systemd-resolved.service
```

## Configuration

Open `configuration.yml` that is located in `group_vars/bind9` and set proper values:

```yml
addresses:
  first: 127.0.0.0
  second: 127.0.1.0
dns1: 9.9.9.9
dns2: 149.112.112.112
```

Here:

```yml
addresses:
  first: 127.0.1.0
  second: 127.0.2.0
```

there is an option to add more addresses that will be added to `acl` configuration in `named.conf.options` e.g:

```yml
addresses:
  first: 127.0.0.0
  second: 127.0.1.0
  third: 127.0.2.0
  fourth: 127.0.3.0
```

and the output for `named.conf.options` will be:

```conf
acl internal {
    127.0.0.0/24;
    127.0.1.0/24;
    127.0.2.0/24;
    127.0.3.0/24;
};
```
