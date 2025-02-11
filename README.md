# Local Services Domain Zone

This repository contains configuration for local services domain zone `*.service.local`.

## Setup

### Start services

```shell
docker compose up -d
```

### Add local domain service zone to systemd-resolved

Use following steps to add local domain service zone to systemd-resolved.

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
sudo docker cp local-service-certs-1:/opt/certs/ca.crt /usr/local/share/ca-certificates/service.local.crt
sudo update-ca-certificates
```

You may need to change `local-service-certs-1` into name of your `certs` container.

> [!Note]
> This step should be done after every build  of the `certs` container.
