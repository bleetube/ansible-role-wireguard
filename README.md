# Ansible Role: Wireguard

This Ansible Role manages wireguard configuration.

## Requirements

None.

## Platforms

Any distro with a repository that has wireguard-tools. It has only been tested on the following:

* Ubuntu 22.04
* Debian 11

### Legacy systems

For distros that do not have the package in their repositories, see the [official installation instructions](https://www.wireguard.com/install/) for your specific distro.

For example, the older Debian 10 buster basically needs you to build the module for the kernel using dkms:

```bash
apt-get install linux-headers-$(uname -r)
apt-get reinstall wireguard-dkms
```

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

```yaml
wireguard_public_key: '..'
wireguard_private_key: '..'
wireguard_subnet: 10.0.0.0/24
wireguard_address: 10.0.0.1/24
wireguard_listen_port: 42069
wireguard_peers:
  - { allowed_ips: '10.0.0.100/32', public_key: '..' }
```

## Secrets

I use [pass](https://www.passwordstore.org/) as a local secret store, which keeps credentials outside of any source code repository. To add credentials for a new host, generate a new key pair for a host `example.acme.com`:

```bash
key=$(wg genkey)
echo $key | pass insert -e example.com/hostname/WIREGUARD_PRIVATE_KEY
echo $key | wg pubkey
unset key
```

Then add an entry to your `.env` so you can source the private key before the playbook runs.

```bash
export example_WIREGUARD_PRIVATE_KEY=$(pass acme.com/example/WIREGUARD_PRIVATE_KEY)
```

## Example Playbook

```yaml
- hosts: all
  roles:
    - bleetube.wireguard
```