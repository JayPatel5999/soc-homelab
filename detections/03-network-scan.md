# Network scan

| Field | Value |
|---|---|
| MITRE ATT&CK | T1046, Network Service Discovery |
| Status | Planned |
| Date run | |
| Attacker | Kali, 10.10.40.10 |
| Target | Lab core subnet, 10.10.30.0/24 |
| Log sources | OPNsense firewall logs and Suricata, forwarded to Wazuh |

## 1. Goal

Detect reconnaissance. Scanning is usually the first visible step of an intrusion, and it shows whether the firewall and IDS are producing useful logs.

## 2. Setup

- Suricata enabled on the Attack zone interface in OPNsense.
- OPNsense sending firewall and Suricata logs to Wazuh over syslog.
- Firewall logging turned on for blocked traffic.

## 3. Attack

Run from Kali against the lab only.

```
nmap -sS -p 1-1000 10.10.30.0/24
nmap -sV -p 22,80,445,3389 10.10.30.10
```

## 4. Evidence

| Source | What to look for | Event or rule |
|---|---|---|
| OPNsense firewall log | One source hitting many ports and hosts in seconds | Block and pass entries |
| Suricata | Scan-related alerts | Depends on the rule set enabled |
| Wazuh | Forwarded firewall and IDS events | Confirm decoders handle the format |

## 5. Detection

To fill in after the run. Note which of the two scans was noticed.

## 6. Triage steps

1. Identify the scanning source and the set of ports and hosts touched.
2. Check whether anything answered on an open port.
3. Look for follow-up connections to a port that answered.

## 7. Tuning and gaps

- Internal vulnerability scanners cause similar traffic, so a real SOC needs an allowlist.
- Compare a fast scan with a slow scan and see whether the slow one slips through.

## 8. Lessons learned

To fill in after the run.
