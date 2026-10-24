# Network and IP plan

## Physical path

```
ISP -> Home router -> Wi-Fi extender -> Ethernet -> Proxmox host (vmbr0)
Home router <- Ethernet -> Raspberry Pi (Pi-hole)
```

The link from router to extender is wireless, so the lab's upload and download speed is limited by that link. If a cable to the main router ever becomes possible, use it.

## VLANs and subnets

| VLAN ID | Name | Subnet | Gateway | DHCP range |
|---|---|---|---|---|
| none | Upstream | Home router range | Home router | Home router |
| 20 | Home | 10.10.20.0/24 | 10.10.20.1 | 10.10.20.100 to .199 |
| 30 | Lab core | 10.10.30.0/24 | 10.10.30.1 | 10.10.30.100 to .199 |
| 40 | Attack zone | 10.10.40.0/24 | 10.10.40.1 | 10.10.40.100 to .199 |

## Static addresses

| Host | VLAN | Address | Notes |
|---|---|---|---|
| Jellyfin | 20 | 10.10.20.10 | LXC or VM |
| NAS (Samba) | 20 | 10.10.20.11 | Backed by the 2 TB HDD |
| Tailscale subnet router | 20 | 10.10.20.12 | Advertises 10.10.20.11/32 only |
| Wazuh server | 30 | 10.10.30.5 | Dashboard on HTTPS |
| Domain controller | 30 | 10.10.30.10 | Windows Server 2022 |
| Windows client | 30 | 10.10.30.20 | Domain joined |
| Kali | 40 | 10.10.40.10 | Attacker |
| Linux target | 40 | 10.10.40.20 | SSH enabled, weak by design |
| Windows target | 40 | 10.10.40.30 | Optional |

## Firewall rules (default deny, then allow what is listed)

| From | To | Action | Why |
|---|---|---|---|
| Home | Internet | Allow | Normal use |
| Home | Lab core, Attack zone | Block | Keep lab away from home services |
| Upstream (my devices) | Jellyfin 8096, NAS 445 | Allow, source limited to home LAN | Watch media and reach files |
| Upstream | Lab core, Attack zone | Block | Lab is not reachable from home |
| Lab core | Internet | Allow | Updates and rule feeds |
| Lab core | Attack zone | Block | Only replies to attack traffic |
| Attack zone | Lab core | Allow only ports a scenario needs, enabled during the test | Attack traffic that must be detected |
| Attack zone | Wazuh 10.10.30.5 ports 1514 and 1515 | Allow | Agent on the Linux target |
| Attack zone | Home, Upstream, Internet | Block | No escape from the isolated zone |
| OPNsense | Wazuh 10.10.30.5 UDP 514 | Allow | Firewall and Suricata logs |

Because OPNsense's WAN sits on a private network, turn off "Block private networks" on the WAN interface or the allow rules for home devices will not work.

## DNS

| Zone | Resolver |
|---|---|
| Upstream and Home | Pi-hole, via the home router |
| Lab core and Attack zone | OPNsense Unbound, no filtering |

## Remote access

Tailscale advertises only 10.10.20.11/32. No lab address is routed over Tailscale.

## Proxmox setup notes

1. Create `vmbr1` with no physical port and enable VLAN aware.
2. Give the OPNsense VM two network devices: one on `vmbr0` (WAN) and one on `vmbr1` with no VLAN tag (trunk).
3. Create VLAN interfaces 20, 30, and 40 inside OPNsense on the trunk device.
4. Give every other VM one network device on `vmbr1` and set its VLAN tag to 20, 30, or 40.

## If a managed switch is added later

TL-SG105E plan for later. Keep one port in the default VLAN for admin access before enabling VLANs, so a bad trunk setting cannot lock out the switch page.

| Port | Device |
|---|---|
| 1 | Uplink to extender |
| 2 | Proxmox host |
| 3 | Raspberry Pi |
| 4 and 5 | Spare |
