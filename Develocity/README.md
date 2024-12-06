# Install Develocity on Ubuntu

## VM Setup

Create a VM with 8 vCPU, 32GB RAM, 750 GB Disk using Ubuntu Server

Configure network as follows:

- IP Address: 192.168.128.140
- Netmask: 255.255.255.0
- Gateway: 192.168.128.1
- DNS: 192.168.128.1

(Proxmox Agent)
```shell
sudo apt install qemu-guest-agent
```

Shutdown, restart the VM. Reboot is not enough.

Install Homebrew: https://brew.sh/

Install K9S

```shell
brew install k9s
```

```shell
sudo timedatectl set-timezone America/Indiana/Indianapolis
```

Follow the instructions here: https://docs.gradle.com/develocity/helm-standalone-installation/current/

## Install K3s

```shell
curl -sfL https://get.k3s.io | sh -
sudo chown $UID /etc/rancher/k3s/k3s.yaml
mkdir -p "${HOME}/.kube"
ln -sf /etc/rancher/k3s/k3s.yaml "${HOME}/.kube/config"
kubectl get namespace
```

## Install Helm

```shell
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
helm repo add gradle https://helm.gradle.com/
helm repo update gradle
helm search repo gradle-enterprise
```

## Install Develocity

```shell
helm install \
    --create-namespace --namespace develocity \
    ge-standalone \
    gradle/gradle-enterprise-standalone \
    --values values.yaml \
    --set-file global.license.file=./develocity.license
```

```shell
helm list --namespace develocity
curl -sw \\n --fail-with-body --show-error https://develocity.jgb-lab.dev/ping
curl -sw \\n --fail-with-body --show-error http://develocity.jgb-lab.dev/ping
```

```shell
helm upgrade \
    --namespace develocity \
    ge-standalone gradle/gradle-enterprise-standalone \
    --version 2024.2.5 \
    --reuse-values \
    --values values.yaml
```

After install:

1. Changed system password to VMware1!
2. Created admin user jeffgbutler/VMware1!
3. Created CI user ciuser/VMware1!
4. Created CI user Access Key


Setup a maven project:

```shell
./mvnw com.gradle:develocity-maven-extension:1.22.2:init -Ddevelocity.url=https://develocity.jgb-lab.dev
```