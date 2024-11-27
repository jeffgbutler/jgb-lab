# Basic Infrastructure Setup

These infrastructure notes assume Proxmox VE as the hypervisor!

The nested environments use VLANs for isolation. They also require a larger MTU than default. Here's what to change...

## Router/Switch Setup

| Item     | VLAN | IP Address       | MTU  | DHCP     |
|----------|------|------------------|------|----------|
| switch0  | N/A  | None             | 2000 | N/A      |
| VLAN 128 | 128  | 192.168.128.1/24 | 2000 | 10 - 128 |
| VLAN 137 | 137  | 192.168.137.1/24 | 2000 | N/A      |
| VLAN 138 | 138  | 192.168.138.1/24 | 2000 | N/A      |
| VLAN 139 | 139  | 192.168.139.1/24 | 2000 | N/A      |
| VLAN 140 | 140  | 192.168.140.1/24 | 2000 | N/A      |
| VLAN 141 | 141  | 192.168.141.1/24 | 2000 | 10 - 128 |
| VLAN 142 | 142  | 192.168.142.1/24 | 2000 | 10 - 128 |

Switch VLAN Configuration:

| Port | pvid | vid                 |
|------|------|---------------------|
| eth1 | 128  | 137,138,139,140,141 |
| eth2 | 128  | 137,138,139,140,141 |
| eth3 | 128  | 137,138,139,140,141 |
| eth4 | 128  | 137,138,139,140,141 |

## PVE Network Setup

```
auto lo
iface lo inet loopback

iface eno3 inet manual

auto eno1
iface eno1 inet manual

auto eno2
iface eno2 inet manual

iface eno4 inet manual

auto bond0
iface bond0 inet manual
        bond-slaves eno1 eno2
        bond-miimon 100
        bond-mode active-backup
        bond-primary eno1

auto vmbr0
iface vmbr0 inet static
        address 192.168.128.129/24
        gateway 192.168.128.1
        bridge-ports bond0
        bridge-stp off
        bridge-fd 0
        bridge-vlan-aware yes
        bridge-vids 2-4094
```

## PVE Storage Setup

Create directories for the different RAID disks named ssdVolume and hddVolume
