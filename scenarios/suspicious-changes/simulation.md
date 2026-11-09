# Suspicious account changes (T1098)

You make two changes that attackers use to keep access, and you make them in the open so the audit log has something honest to show.

1. Lab User 1 registers one extra MFA method. That is [T1098 Account Manipulation](https://attack.mitre.org/techniques/T1098/): the account's authentication is changed so the attacker can pass MFA later.
2. An admin assigns Lab User 2 the `Reports Reader` role. Adding a directory role is [T1098.003 Additional Cloud Roles](https://attack.mitre.org/techniques/T1098/003/).

`Reports Reader` is a low privilege role. It can read sign-in and audit reports. It is enough to prove the audit event. It is not an admin role.

## Stay inside this tenant

* Only Lab User 1 and Lab User 2.
* The extra MFA method uses a phone or an authenticator app you control.
* Do not assign Global Administrator, Privileged Role Administrator, User Administrator, Security Administrator, Conditional Access Administrator, or Exchange Administrator to a lab user.
* Do not add a guest from another tenant.
* Remove both changes in cleanup the same day.

## Before you start

* You can sign in as Lab User 1 and as your working admin.
* You have already captured the MFA fatigue screenshots, so another Authenticator prompt will not confuse that count. If you have not done part 6, do it first.
* VPN off.

## Steps: register one MFA method

1. Private window. Sign in as Lab User 1. Complete the MFA method you already trust.
2. Open [https://mysignins.microsoft.com/security-info](https://mysignins.microsoft.com/security-info).
3. Select `Add sign-in method`.
4. Add one method you can delete afterwards:
   * Another Microsoft Authenticator registration on a device you control, or
   * A phone number you control.
5. Finish the prompts. Do not add a second extra method.
6. Sign out.

If the page refuses because security defaults or a policy blocks registration, stop and screenshot the message. That refusal is a control working. Write it up. Do not weaken a policy you do not understand just to force the event. You can still do the role assignment.

## Steps: assign Reports Reader

1. In the admin window, go to `Entra ID` > `Roles & admins` > `Roles & administrators`.
2. Search for `Reports Reader`.
3. Open it. Select `Add assignments`.
4. Use these settings:

| Field | Value |
| --- | --- |
| Member | Lab User 2 |
| Assignment type | Active |
| Permanently assigned | Yes |
| Justification | `Lab evidence for T1098. Remove the same day.` |
| Scope | Directory, the default. Do not scope it over the whole tenant's administrative units unless that is the only choice. Record what you used. |

5. Save.
6. Open Lab User 2 > `Assigned roles` and confirm `Reports Reader` is listed.

If the blade only offers an eligible Privileged Identity Management assignment, choose eligible, record that, and still remove it during cleanup. Eligible is not the same audit story as active. The wording in your report should match the screen.

## What you should look for

`Entra ID` > `Monitoring & health` > `Audit logs`.

| Change | Category | Activity name to find |
| --- | --- | --- |
| Extra MFA method | `UserManagement` | `User registered security info` |
| You started the page but the name differs | `UserManagement` | `User started security info registration` or `User registered all required security info` |
| An admin added the method instead | `UserManagement` | `Admin registered security info` |
| Role | `RoleManagement` | `Add member to role` |

These names are from Microsoft's [audit activity reference](https://learn.microsoft.com/entra/identity/monitoring-health/reference-audit-activities). If your tenant uses a longer PIM name, such as `Add member to role in PIM completed (permanent)`, write that exact string in the report. The hunt matches role events with `has "member to role"` so those PIM names still return.

Open each event. Check actor, target, result, and time. The role event's modified properties should name `Reports Reader`.

Then run [detection.kql](detection.kql). Audit streaming does not need Entra ID P1. If `AuditLogs` is connected, this scenario can succeed even when `SigninLogs` is unavailable.

## After the test

Follow [response.md](response.md). You can remove the role and the extra MFA method as soon as the screenshots are saved. Screenshots: checklist items 15, 16, and 17.
