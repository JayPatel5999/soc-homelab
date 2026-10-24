# Password spray against Active Directory

| Field | Value |
|---|---|
| MITRE ATT&CK | T1110.003, Brute Force: Password Spraying |
| Status | Planned |
| Date run | |
| Attacker | Kali, 10.10.40.10 |
| Target | Domain controller, 10.10.30.10 |
| Log sources | Wazuh agent on the domain controller |

## 1. Goal

Detect one password tried against many accounts, which avoids lockouts and looks different from a classic brute force.

## 2. Setup

- Wazuh agent on the domain controller.
- Advanced audit policy enabled for logon failures.
- A list of lab usernames and one test password.
- Firewall rule allowing Attack zone to the DC on the ports needed for this test, enabled only for the run.

## 3. Attack

Run from Kali against the lab domain only.

```
netexec smb 10.10.30.10 -u users.txt -p 'LabPassword1!' --continue-on-success
```

## 4. Evidence

| Source | What to look for | Event or rule |
|---|---|---|
| Domain controller Security log | Event 4625 failed logons, many different accounts, one source address | 4625 |
| Domain controller Security log | Kerberos pre-authentication failures | 4771 |
| Wazuh | Windows logon failure alerts, then a multiple failures alert | Confirm rule IDs in your Wazuh version |

## 5. Detection

To fill in after the run.

## 6. Triage steps

1. Group failures by source address and by account.
2. Many accounts from one source is spraying. One account from many sources is a different pattern.
3. Look for any 4624 success from the same source afterward.
4. Check the lockout policy and which accounts were hit.

## 7. Tuning and gaps

- Compare the alert against the lockout threshold. Spraying is designed to stay under it.
- Note whether Wazuh groups by source or only counts per host.

## 8. Lessons learned

To fill in after the run.
