# Install K3S

## VM Setup

Create a VM with 8 vCPU, 16GB RAM, 500 GB Disk using Ubuntu Server

Configure network as follows:

- IP Address: 192.168.128.160
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

## Install K3s

```shell
curl -sfL https://get.k3s.io | sh -
sudo chown $UID /etc/rancher/k3s/k3s.yaml
mkdir -p "${HOME}/.kube"
ln -sf /etc/rancher/k3s/k3s.yaml "${HOME}/.kube/config"
kubectl get namespace
```

## Install Gatekeeper

```shell
kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/v3.18.2/deploy/gatekeeper.yaml
```

## Install Test Application
