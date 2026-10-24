# SSH brute force

| Field | Value |
|---|---|
| MITRE ATT&CK | T1110.001, Brute Force: Password Guessing |
| Status | Planned |
| Date run | |
| Attacker | Kali, 10.10.40.10 |
| Target | Linux target, 10.10.40.20 |
| Log sources | Wazuh agent on the Linux target, OPNsense firewall logs |

## 1. Goal

Detect repeated failed SSH logins from one source, the most common first sign of password guessing against an exposed service.

## 2. Setup

- Wazuh agent installed on the Linux target and reporting to 10.10.30.5.
- SSH enabled on the target with a test account that has a weak password from a small wordlist.
- Firewall rule allowing Attack zone to Wazuh ports 1514 and 1515 is active.

## 3. Attack

Run from Kali against the lab target only.

```
hydra -l labuser -P small-wordlist.txt ssh://10.10.40.20
```

## 4. Evidence

| Source | What to look for | Event or rule |
|---|---|---|
| Target `/var/log/auth.log` | Many "Failed password" lines from 10.10.40.10 | sshd |
| Wazuh | Failed authentication alerts, then a brute force alert | Expected sshd rules, confirm IDs in your Wazuh version |
| OPNsense | Many short connections from Kali to port 22 | Firewall log |

## 5. Detection

To fill in after the run: which alerts fired, their levels, and the delay.

## 6. Triage steps

1. Open the alert and note the source address, target account, and count.
2. Check whether any login succeeded after the failures.
3. Check whether the source is expected, such as a scanner or admin.
4. Decide: block the source, reset the account, or close as test traffic.

## 7. Tuning and gaps

- A user mistyping a password should not alert. Confirm the threshold.
- Compare what the target log shows against what the firewall shows.

## 8. Lessons learned

To fill in after the run.
