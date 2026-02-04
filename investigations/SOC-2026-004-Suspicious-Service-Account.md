# CASE ID: SOC-2026-004  
## Incident Type: Suspicious Service Account Activity  
**Severity:** Medium  
**Status:** Contained  

---

## 1. Executive Summary

On 07 February 2026, Splunk generated an alert indicating abnormal authentication activity involving service account **svc_backup**.

The account, typically restricted to scheduled backup operations on server **WIN-BACKUP-01**, was observed authenticating interactively to workstation **WIN-CLIENT-12**.

Service accounts are non-interactive by design. The deviation from baseline behavior triggered investigation.

Initial analysis confirmed no approved maintenance activity. The account was temporarily disabled pending review.

---

## 2. Alert Metadata

| Field | Value |
|-------|--------|
| Detection Source | Splunk Enterprise |
| Event Code | 4624 |
| Logon Type | 2 (Interactive) |
| Account Name | svc_backup |
| Source Host | WIN-CLIENT-12 |
| Time | 2026-02-07 22:41 UTC |

---

## 3. Initial Triage

- Service account authenticated interactively
- Logon Type 2 indicates local console login
- Account normally restricted to scheduled task execution
- No approved change or IT ticket found
- Authentication occurred outside normal backup window

Assessment: Abnormal service account usage requiring full investigation.

---

## 4. Technical Investigation

### 4.1 Service Account Login Review

SPL Query Used:

index=wineventlog EventCode=4624 Account_Name="svc_backup"
| table _time, host, Logon_Type, Source_Network_Address

Findings:

- Interactive login on WIN-CLIENT-12
- No historical record of interactive logons
- Authentication outside standard backup schedule (02:00 UTC)
- Source IP internal corporate network

---

### 4.2 Privilege Validation

SPL Query Used:

index=wineventlog EventCode=4672 Account_Name="svc_backup"
| table _time, host, Privileges

Findings:

- Special privileges assigned (SeBackupPrivilege, SeRestorePrivilege)
- Elevated capability to access sensitive files

---

### 4.3 Lateral Movement Check

SPL Query Used:

index=wineventlog Account_Name="svc_backup"
| stats count by host

Findings:

- No additional hosts accessed
- No abnormal file share access detected
- No suspicious process execution observed

---

## 5. MITRE ATT&CK Mapping

| Tactic | Technique | ID |
|--------|----------|----|
| Persistence | Valid Accounts | T1078 |
| Privilege Escalation | Abuse of Privileges | T1068 |

---

## 6. Impact Assessment

- Service account misused for interactive login
- Elevated privileges present
- No lateral movement confirmed
- No persistence mechanisms identified

Risk Rating: Medium

Although no confirmed compromise occurred, misuse of privileged service accounts presents significant security risk.

---

## 7. Response Actions

- Immediate disablement of svc_backup
- Forced credential reset
- Review of scheduled task configurations
- Investigation of workstation WIN-CLIENT-12
- Enabled service account interactive logon restrictions via Group Policy

---

## 8. Detection Engineering Improvements

- Alert on interactive logon (Type 2 or 10) for service accounts
- Correlate service accounts with workstation logins
- Implement service account baseline behavior monitoring
- Enforce "Deny log on locally" policy for all service accounts

---

## 9. Escalation Decision

Contained at Tier 1.

Reason: No evidence of lateral movement or confirmed malicious payload execution.

Incident documented and monitored for recurrence.

---

## 10. Evidence References

- ../screenshots/SOC-2026-004-alert.png  
- ../screenshots/SOC-2026-004-auth-log.png  
- ../screenshots/SOC-2026-004-privileges.png  

