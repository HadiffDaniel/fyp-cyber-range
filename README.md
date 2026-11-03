# Scalable, Isolated Virtualized Security Lab for Classroom Cyber Attack and Defense Training

Final Year Project, Bachelor of Computer Science (Network and Security).

A scenario-driven classroom cyber range built on five desktop PCs with OpenStack.
Students select a training scenario; the system automatically provisions isolated
attacker, defender and victim VMs; the lecturer evaluates and resets the environment
for the next group.

**Provision -> Isolate -> Train -> Assess -> Reset -> Reuse**

## Architecture

| Node | Role | Hardware |
|---|---|---|
| PC1 `pc1-ctl` | OpenStack controller + network node | i7-6700, 16 GB |
| PC2 `pc2-cmp` | Compute | i7-6700, 16 GB |
| PC3 `pc3-cmp` | Compute | i5-6500, 8 GB |
| PC4 `pc4-cmp` | Compute | i5-6500, 8 GB |
| PC5 `pc5-jump` | Jump Host + Cyber Range Portal (DMZ) | i5-6500, 8 GB |

Switch: Force10 S50V with VLAN segmentation.

| VLAN | Name | Subnet | Purpose |
|---|---|---|---|
| 10 | MGMT | 192.168.10.0/24 | OpenStack management / internal API |
| 20 | DMZ | 172.16.20.0/24 | Jump Host, student-facing |
| 30 | API | 192.168.30.0/24 | OpenStack public API, Jump Host only |
| 40 | STUDENT | 192.168.40.0/24 | Student devices (wired + Wi-Fi) |
| 50 | TUNNEL | 10.0.50.0/24 | VXLAN overlay for scenario VMs |
| 60 | PROVIDER | - | Neutron external interface |

Each student group gets its own isolated VXLAN network (10.10.n.0/24) with no router.

## Stack

- Ubuntu Server 24.04 LTS
- OpenStack 2026.1 deployed with Kolla-Ansible (Keystone, Nova, Neutron, Glance, Heat, Horizon)
- Scenarios as OpenStack Heat templates
- Portal: Python Flask + openstacksdk

## Repository layout

| Folder | Contents |
|---|---|
| `network/switch` | Switch configuration |
| `network/netplan` | Host network configuration per node |
| `network/nftables` | Build-phase NAT rules on PC1 |
| `openstack/kolla` | Kolla-Ansible `globals.yml` and inventory |
| `scenarios` | Heat templates for training scenarios |
| `portal` | Cyber Range Management Portal |
| `docs` | Design notes and test evidence |

Secrets (`passwords.yml`, `clouds.yaml`) are excluded via `.gitignore`.

## Status

- [x] Network design and VLAN segmentation
- [x] OpenStack deployed and verified (VMs boot, cross-node VXLAN, no external reachability)
- [ ] Scenario images and Heat templates
- [ ] Cyber Range Management Portal
- [ ] Isolation, provisioning and reset testing
