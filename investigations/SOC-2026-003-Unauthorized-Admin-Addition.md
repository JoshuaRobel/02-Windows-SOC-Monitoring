# CASE ID: SOC-2026-003  
## Incident Type: Privilege Escalation — Unauthorized Admin Group Addition  
**Severity:** High  
**Status:** Escalated  

---

## 1. Executive Summary

On 05 February 2026, Splunk generated an alert indicating that user account **j.smith** was added to the **Domain Admins** group.

Event ID 4728 confirmed the group membership change occurred on Domain Controller **DC-01**.

No approved change request was found. Due to the high privilege nature of the Domain Admins group, the incident was escalated immediately.

---

## 2. Alert Metadata

| Field | Value |
|-------|--------|
| Detection Source | Splunk Enterprise |
| Event ID | 4728 |
| Domain Controller | DC-01 |
| Affected User | j.smith |
| Initiating Account | admin.local |
| Time | 2026-02-05 11:22 UTC |

---

## 3. Initial Triage

- Confirmed Event ID 4728 (member added to security-enabled global group)
- Target group: Domain Admins
- No approved IT change ticket
- Initiating account: admin.local

Assessment: Potential unauthorized privilege escalation.

---

## 4. Technical Investigation

### 4.1 Group Membership Change Query

index=wineventlog EventCode=4728
| table _time, Subject_Account_Name, Member_Name, Group_Name

Findings:
- Member Added: j.smith
- Group: Domain Admins
- Initiated by: admin.local

### 4.2 Authentication Review

index=wineventlog EventCode=4624 Account_Name="admin.local"
| table _time, host, Source_Network_Address

Findings:
- admin.local logged in from workstation WIN-CLIENT-07
- Source IP internal corporate network

## 5. MITRE ATT&CK Mapping

| Tactic | Technique | ID |
|--------|----------|----|
| Privilege Escalation | Valid Accounts | T1078 |

---

## 6. Impact Assessment

- Domain Admin privileges granted
- Full AD control risk
- No lateral movement observed
- No suspicious logins following addition

Risk Rating: **High**

---

## 7. Response Actions

- Immediate removal of j.smith from Domain Admins group
- Temporary disablement of admin.local
- Forced password reset
- Full review of recent privileged activity
- Initiated domain-wide privilege audit

---

## 8. Escalation Decision

Escalated to Security Engineering and IT Management.

Reason: Unauthorized assignment of Domain Admin privileges presents critical enterprise risk.

---

## 9. Evidence References

- ../screenshots/SOC-2026-003-alert.png  
- ../screenshots/SOC-2026-003-group-change.png  
- ../screenshots/SOC-2026-003-auth-review.png  
