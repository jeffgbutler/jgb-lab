# Install Harbor on Ubuntu

## VM Setup

Create a VM with 4 vCPU, 8GB RAM, 160 GB Disk using Ubuntu Server

Configure network as follows:

- IP Address: 192.168.128.133
- Netmask: 255.255.255.0
- Gateway: 192.168.128.1
- DNS: 192.168.128.1

```shell
sudo apt install qemu-guest-agent
```

## Install Docker

Follow the instructions here: https://docs.docker.com/engine/install/ubuntu/

Follow post install instructions here: https://docs.docker.com/engine/install/linux-postinstall/

## Install Harbor

Create directory for certificates:
```shell
sudo mkdir -p /etc/docker/certs.d/harbor.jbcodes.net
```

Copy Harbor certificates as follows:

   - /etc/letsencrypt/live/harbor.jbcodes.net/fullchain.pem ->  /etc/docker/certs.d/harbor.jbcodes.net/harbor.jbcodes.net.cert
   - /etc/letsencrypt/live/harbor.jbcodes.net/privkey.pem -> /etc/docker/certs.d/harbor.jbcodes.net/harbor.jbcodes.net.key

Mac:

```shell
sudo cat /etc/letsencrypt/live/harbor.jbcodes.net/fullchain.pem
```

Harbor: 

```shell
sudo vim /etc/docker/certs.d/harbor.jbcodes.net/harbor.jbcodes.net.cert
```

Mac:

```shell
sudo cat /etc/letsencrypt/live/harbor.jbcodes.net/privkey.pem
```

Harbor:

```shell
sudo vim /etc/docker/certs.d/harbor.jbcodes.net/harbor.jbcodes.net.key
```

Then

- `sudo mkdir /harbor /data`
- `cd /harbor`
- `sudo curl -sLO https://github.com/goharbor/harbor/releases/download/v2.12.2/harbor-offline-installer-v2.12.2.tgz`
- `sudo tar xvf harbor-offline-installer-v2.12.2.tgz --strip-components=1`
- `sudo cp harbor.yml.tmpl harbor.yml`
- `sudo vim harbor.yml`

Set/Update the following:

- hostname: harbor.jbcodes.net
- https:
  - certificate: /etc/docker/certs.d/harbor.jbcodes.net/harbor.jbcodes.net.cert
  - private_key: /etc/docker/certs.d/harbor.jbcodes.net/harbor.jbcodes.net.key

`sudo ./install.sh --with-trivy`

## Rotate Certificates on Harbor

1. Copy new certificates as follows:

   - /etc/letsencrypt/live/harbor.jbcodes.net/fullchain.pem ->  /etc/docker/certs.d/harbor.jbcodes.net/harbor.jbcodes.net.cert
   - /etc/letsencrypt/live/harbor.jbcodes.net/privkey.pem -> /etc/docker/certs.d/harbor.jbcodes.net/harbor.jbcodes.net.key

1. `cd /harbor`
1. `docker compose down -v`
1. `./prepare --with-trivy`
1. `docker compose up -d`
