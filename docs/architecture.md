# Architecture and design decisions

This page explains why the lab is built the way it is. Decisions are written so a reviewer can see the reasoning, not just the result.

## One host, everything virtual

The whole lab runs on one Dell Precision 3420 SFF. All VLANs exist inside Proxmox, so no VLAN-capable switch is required to get started. A TP-Link TL-SG105E is planned for later, when the Raspberry Pi, an access point, or more wired devices join.

## Proxmox bridges

| Bridge | Physical port | Purpose |
|---|---|---|
| `vmbr0` | Onboard Ethernet | Upstream network. Proxmox management IP and the OPNsense WAN interface |
| `vmbr1` | None | VLAN-aware internal bridge. Carries VLANs 20, 30, and 40 between VMs |

The Wi-Fi card is not used for the lab. Linux bridges cannot pass VM traffic over Wi-Fi, so the host connects by Ethernet to the Wi-Fi extender, and the extender handles the wireless link to the home router.

## Why OPNsense

OPNsense routes between VLANs, runs DHCP and DNS per VLAN, and includes Suricata for intrusion detection. It also has a Wazuh agent plugin and sends logs to a remote syslog server, which makes the firewall a log source for the SIEM. pfSense would work the same way for the concepts shown here.

## Why separate Lab core and Attack zone

Traffic inside one VLAN never crosses the firewall, so it would never reach Suricata or firewall logs. Putting Kali and the vulnerable targets in their own VLAN forces every attack through OPNsense, which gives the detection write-ups more evidence to work with. It also keeps the weak machines away from Wazuh and Active Directory unless a scenario needs them.

## Double NAT

OPNsense sits behind the home router and the extender, so its WAN address is a private one. This is fine for a lab. Two side effects are handled in [network-and-ip-plan.md](network-and-ip-plan.md): the WAN "block private networks" option must be off, and home devices reach Jellyfin through explicit firewall rules.

## DNS

- The home network uses Pi-hole on the Raspberry Pi.
- The Home VLAN forwards DNS to Pi-hole through OPNsense.
- The Lab core and Attack zone use OPNsense's own resolver, so ad blocking never hides or alters attack traffic.

## Remote access

Tailscale runs in a small container in the Home VLAN as a subnet router. It advertises only the NAS address as a single /32 route. The lab VLANs are never reachable over Tailscale, so vulnerable machines are not exposed remotely.

## Storage

| Disk | Use |
|---|---|
| 1 TB NVMe | Proxmox OS, VM disks, container disks |
| 2 TB WD Red HDD | NAS share and media library (planned) |

## Suggested memory budget (32 GB)

Not every machine runs at once. These are starting points to adjust after real use.

| Workload | RAM |
|---|---|
| Proxmox host | 3 GB |
| OPNsense | 2 GB |
| Wazuh server | 8 GB |
| Windows Server 2022 DC | 4 GB |
| Windows client | 4 GB |
| Kali | 4 GB |
| Vulnerable targets | 2 GB each, started on demand |
| Jellyfin, NAS, Tailscale containers | 2 GB combined |

## Jellyfin transcoding

The i7-6700 has an integrated GPU with Intel Quick Sync, so basic H.264 transcoding works without a discrete card. A low-profile GPU stays on the wish list for AV1 and heavier loads later.

## What is deliberately out of scope for now

- Splunk. Wazuh covers the SIEM role, and Splunk may be added later for comparison.
- Clustering and high availability. One host is enough to learn the concepts.
- Exposing any service to the public internet.
