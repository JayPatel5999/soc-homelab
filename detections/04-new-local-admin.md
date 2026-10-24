# New local administrator account

| Field | Value |
|---|---|
| MITRE ATT&CK | T1136.001, Create Account: Local Account |
| Status | Planned |
| Date run | |
| Attacker | Simulated from an admin session on the Windows client, 10.10.30.20 |
| Target | Windows client, 10.10.30.20 |
| Log sources | Wazuh agent on the Windows client |

## 1. Goal

Detect persistence through a new account added to the local Administrators group, a common step after an attacker gains access.

## 2. Setup

- Wazuh agent on the Windows client.
- Audit policy enabled for user account management and security group management.
- Optional: Sysmon installed for richer process logs.

## 3. Attack

Run in an elevated prompt on the lab client only, then clean up.

```
net user labtest 'TempPass123!' /add
net localgroup Administrators labtest /add
net user labtest /delete
```

## 4. Evidence

| Source | What to look for | Event or rule |
|---|---|---|
| Windows Security log | User account created | 4720 |
| Windows Security log | Member added to a local security group | 4732 |
| Wazuh | Account creation and group change alerts | Confirm rule IDs in your Wazuh version |

## 5. Detection

To fill in after the run.

## 6. Triage steps

1. Find who created the account and from which session.
2. Check whether the account was added to Administrators.
3. Look at what ran just before, such as a new shell or remote logon.
4. Decide: disable the account, isolate the host, or close as an approved change.

## 7. Tuning and gaps

- Legitimate IT changes create the same events, so a real SOC compares against change tickets.
- Check how quickly the delete event (4726) shows up.

## 8. Lessons learned

To fill in after the run.
