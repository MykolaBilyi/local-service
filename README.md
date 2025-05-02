# Local Services Domain Zone

This repository contains configuration for local services domain zone `*.service.local`.

## Setup

### Configuration

Service domain zone is configured in [.env](.env) file. You can change it to a domain name which suits your style.

> [!Note]
> Browsers do not accept second-level wildcard certificates, therefore it is advised against using single (tld) level domain name, e.g `.home` or `.localhost`

```env
DOMAIN=service.local
```

### Start services on Ubuntu-like systems

For Ubuntu-like systems (e.g. Linux Mint), you can use next command

```shell
docker compose --profile ubuntu up -d
```

This does the following:

- Starts services
- Configures `systemd-resolved` to resolve `*.service.local` domain names by adding a configuration file to `/etc/systemd/resolved.conf.d/`
- Adds local certificate authority to system trust store by adding a certificate file to `/usr/local/share/ca-certificates/`

To apply the changes, restart `systemd-resolved` service and update ca certificates:

```shell
sudo systemctl restart systemd-resolved
sudo update-ca-certificates
```

### Start services on other systems

For other systems, you can use next command

```shell
docker compose up -d
```

You will have to figure out how to configure local DNS and certificate authority on your system.

### Add local domain service zone to `systemd-resolved`

If your system uses `systemd-resolved`, you can use following steps to add local domain service zone to systemd-resolved.

```shell
sudo mkdir -p /etc/systemd/resolved.conf.d
sudo editor /etc/systemd/resolved.conf.d/10-dnsmasq-service.conf
```

Add following content to the file.

```ini
[Resolve]
DNS=127.0.0.1
Domains=~service.local
```

Restart `systemd-resolved` service.

```shell
sudo systemctl restart systemd-resolved
```

### Add local certificate authority to system trust store

This is useful for services which use system trust store to validate certificates. Chrome and Firefox use their own trust store.

> [!Warning]
> Local cert authority adds another attack vector to your system. Only use this if you acknowledge the risks.

```shell
sudo docker cp local-service-cert-1:/opt/certs/ca.crt /usr/local/share/ca-certificates/service.local.crt
sudo update-ca-certificates
```

You may need to change `local-service-cert-1` into real name of your `cert` container.

> [!Note]
> This step should be done after every build  of the `cert` container.
