# Scenario title

| Field | Value |
|---|---|
| MITRE ATT&CK | Technique ID and name |
| Status | Planned, In progress, or Complete |
| Date run | |
| Attacker | Machine and address |
| Target | Machine and address |
| Log sources | Which agents or devices should see it |

## 1. Goal

One or two sentences: what behaviour am I trying to detect and why it matters to a SOC.

## 2. Setup

Anything that must be true before the test (agent installed, audit policy enabled, firewall rule opened for this test).

## 3. Attack

The exact commands run, in order, against lab machines only.

```
command here
```

## 4. Evidence

Where the activity shows up. Add screenshots to `docs/img/` and link them.

| Source | What to look for | Event or rule |
|---|---|---|
| | | |

## 5. Detection

- Which alert fired, at what level, and how long after the attack started
- Whether it fired the way I expected or needed tuning

## 6. Triage steps

How an analyst would investigate this alert, in order.

1.
2.
3.

## 7. Tuning and gaps

- False positives I would expect in a real environment
- Anything the SIEM missed and what log source or rule would close the gap

## 8. Lessons learned

Two or three honest sentences.
