# Install Harbor on Ubuntu

## VM Setup

Create a VM with 4 vCPU, 8GB RAM, 160 GB Disk using Ubuntu Server

Configure network as follows:

- IP Address: 192.168.128.133
- Netmask: 255.255.255.0
- Gateway: 192.168.128.1
- DNS: 192.168.128.1

## Install Docker

Follow the instructions here: https://docs.docker.com/engine/install/ubuntu/

Follow post install instructions here: https://docs.docker.com/engine/install/linux-postinstall/

## Install Harbor

`sudo mkdir -p /etc/docker/certs.d/harbor.jgb-lab.dev`

Copy Harbor certificates as follows:

   - /etc/letsencrypt/live/harbor.jgb-lab.dev/fullchain.pem ->  /etc/docker/certs.d/harbor.jgb-lab.dev/harbor.jgb-lab.dev.cert
   - /etc/letsencrypt/live/harbor.jgb-lab.dev/privkey.pem -> /etc/docker/certs.d/harbor.jgb-lab.dev/harbor.jgb-lab.dev.key

Mac:

```shell
sudo cat /etc/letsencrypt/live/harbor.jgb-lab.dev/fullchain.pem
```

Harbor: 

```shell
sudo vim /etc/docker/certs.d/harbor.jgb-lab.dev/harbor.jgb-lab.dev.cert
```

Mac:

```shell
sudo cat /etc/letsencrypt/live/harbor.jgb-lab.dev/privkey.pem
```

Harbor:

```shell
sudo vim /etc/docker/certs.d/harbor.jgb-lab.dev/harbor.jgb-lab.dev.key
```

Then

- `sudo mkdir /harbor /data`
- `cd /harbor`
- `sudo curl -sLO https://github.com/goharbor/harbor/releases/download/v2.11.2/harbor-offline-installer-v2.11.2.tgz`
- `sudo tar xvf harbor-offline-installer-v2.11.2.tgz --strip-components=1`
- `sudo cp harbor.yml.tmpl harbor.yml`
- `sudo vim harbor.yml`

Set/Update the following:

- hostname: harbor.jgb-lab.dev
- https:
  - certificate: /etc/docker/certs.d/harbor.jgb-lab.dev/harbor.jgb-lab.dev.cert
  - private_key: /etc/docker/certs.d/harbor.jgb-lab.dev/harbor.jgb-lab.dev.key

`sudo ./install.sh --with-trivy`

## Rotate Certificates on Harbor

1. Copy new certificates as follows:

   - /etc/letsencrypt/live/harbor.jgb-lab.dev/fullchain.pem ->  /etc/docker/certs.d/harbor.jgb-lab.dev/harbor.jgb-lab.dev.cert
   - /etc/letsencrypt/live/harbor.jgb-lab.dev/privkey.pem -> /etc/docker/certs.d/harbor.jgb-lab.dev.net/harbor.jgb-lab.dev.key

1. `cd /harbor`
1. `docker compose down -v`
1. `./prepare --with-trivy`
1. `docker compose up -d`
