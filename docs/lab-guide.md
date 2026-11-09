# Lab guide

Run this lab on a Microsoft Entra ID tenant you own, with test users you create. Do not point any step at an employer tenant, a customer tenant, a university tenant, or any account that is not yours.

The attack steps are limited to accounts in this lab. There is no script, wordlist, or tool to download. You type a few wrong passwords yourself, you deny a few MFA prompts on your own phone, and you change your own VPN country once.

If a step conflicts with the screen in your portal, follow the label you see and write down the difference in the report. Portal names move. The settings in this guide are the ones that matter.

## Time

| Part | Active time | Waiting |
| --- | --- | --- |
| 0. Read warnings and collect prerequisites | 20 to 30 minutes | None |
| 1. Users, group, emergency accounts, MFA registration | 45 to 70 minutes | None |
| 2. Budget, daily cap, data connector | 25 to 40 minutes | None |
| 3. First log row | 15 minutes | 15 to 60 minutes is common. Stop generating sign-ins while you wait. If nothing has arrived after 3 hours, use Troubleshooting. |
| 4. Password spray | 30 to 45 minutes | Up to 60 minutes for the row to reach Sentinel |
| 5. Impossible travel | 30 to 45 minutes | Up to 60 minutes |
| 6. MFA fatigue | 30 to 45 minutes | Up to 60 minutes |
| 7. Suspicious changes | 30 to 40 minutes | Up to 60 minutes |
| 8. Conditional Access designs | 60 to 90 minutes if you have P1 and deploy report only. About 30 minutes if you only review the designs. | None |
| 9. Report | 60 to 120 minutes | None |
| 10. Cleanup | 30 to 45 minutes | Check the bill the next day |

Active work is about 6 to 9 hours. Split it across two or three days so you are not rushing the lockout checks.

## Cost warning

The Sentinel workspace already exists for [Sentinel-Home-SOC-Lab](https://github.com/Sasa021/Sentinel-Home-SOC-Lab). Reuse it.

* Microsoft Sentinel includes a 31 day waiver of the first 10 GB per day when it is first enabled on a workspace. Your home SOC lab may already have started that clock. Open Sentinel, then `News & guides`, then `Free trial`, and write down the dates. Source: [Sentinel billing](https://learn.microsoft.com/azure/sentinel/billing).
* `SigninLogs` and `AuditLogs` are paid after that waiver. Alerts and Office 365 audit tables can be free. Entra sign-in and audit tables are not those tables.
* Read the price for your region on the [Sentinel pricing page](https://azure.microsoft.com/pricing/details/microsoft-sentinel/) before you connect logs. Do not rely on a price copied from a blog.
* In part 2 you set a budget alert, and you deal with the daily cap, before the connector is enabled.
* Do not enable `Non-interactive user sign-in logs`, `Service principal sign-in logs`, or `Managed identity sign-in logs`.
* Do not create a virtual machine, a second workspace, or another region for this lab. The VPN test uses a VPN client, not Azure compute.
* A licence trial that asks for a credit card can turn into a paid subscription. Read the screen. Decline it if you do not want that risk. If you accept it, record the end date and cancel it in Microsoft 365 admin center, `Billing`, `Your products`, before the trial ends.
* Cleanup in part 10 does not delete the Sentinel resource group.

## Lockout warning

Conditional Access and security defaults can both stop a sign-in. A policy scoped to all users, set to `On`, with a block or an MFA method you cannot pass, can lock every admin out.

* Create two emergency access accounts in part 1, before you change any policy.
* Sign in as each of them and confirm success before part 8.
* Keep a browser window signed in as your working admin for the whole of part 8. Use a different browser, or a private window, for tests.
* New Conditional Access policies stay in `Report-only`. `Report-only` does not block anyone.
* Do not set a policy to `On` in this lab unless you have already seen a report only result, the emergency accounts are excluded, and a test user still behaves the way you expect.
* Security defaults and Conditional Access cannot both be in force. If you turn security defaults off, create the require MFA policy in the same sitting, in report only at first, and do not close the admin session.
* Password spray can temporarily lock the lab users. Stop when you see a lockout message. Lockout of a lab user is not an emergency. Lockout of every admin is.

Microsoft's emergency access guidance is here: [Manage emergency access admin accounts](https://learn.microsoft.com/entra/identity/role-based-access-control/security-emergency-access).

## Rules for every scenario

* Your tenant, your test users, your phone, your VPN.
* No third party tenant, no friend, no employer, no production mailbox.
* No spray tool, no MFA bombing tool, no copied breach wordlist, no cloud function that retries logins.
* Stop at the attempt limits in each scenario.
* If Microsoft throttles you, stop and record the throttle. That is a finding.
* Store passwords in a password manager. Do not put them in this repository, in a screenshot, or in a commit message.

## Prerequisites

You need:

* A Microsoft account that can open the Entra admin center as a Global Administrator of a tenant you own.
* The Azure subscription that hosts the Sentinel workspace from Sentinel-Home-SOC-Lab, with you in the Microsoft Sentinel Contributor role on that workspace.
* Security Administrator, or an equivalent role, so you can change diagnostic settings. Global Administrator covers this in a personal lab.
* A phone with Microsoft Authenticator, for at least your admin, both emergency accounts, and Lab User 1.
* A VPN client from a provider you already trust. The Proton VPN free plan is a no cost option with a small set of exit countries. Use it only for part 5, then disconnect. A free VPN can see the traffic you send through it. Do not use it for banking or personal mail. Do not use Tor, an open proxy list, or a host you do not control.
* A password manager.
* A private browser profile, so lab sign-ins are not mixed with your admin session.
* About two or three free days on the calendar, so log delay does not push you into a paid trial you forgot to cancel.

You do not need:

* Entra ID P2. Parts that need it are marked, and the lab is complete without them.
* A paid attack platform.
* A hardware security key. A passkey on your phone or Windows Hello is enough if you want a phishing resistant method for the emergency accounts. If you cannot register one, Authenticator on a device you control is acceptable for this lab. Say which method you used in the report.

Licence paths, in the order to consider them:

1. Use the tenant you already have, if you own it and it has no real organisation data.
2. If you need Conditional Access or sign-in log streaming, check whether you already have Entra ID P1. Microsoft 365 Business Premium includes P1. Microsoft 365 E3 and E5 include P1, and E5 includes P2.
3. If you are eligible for a Microsoft 365 Developer Program E5 sandbox, that sandbox is a strong fit and includes a full Entra licence set. Many individual learners are not eligible. Check the [Developer Program FAQ](https://learn.microsoft.com/office/developer-program/microsoft-365-developer-program-faq) and stop if the signup asks you to buy something.
4. A Business Premium trial or an Entra P1 or P2 trial is optional. Read the payment terms first.

Record the licence you actually have. You will screenshot it in checkpoint 1.

## Part 1. Prepare the tenant

**Screenshot checkpoint:** 1, 2, and 3 in [screenshot-checklist.md](screenshot-checklist.md).

### 1.1 Confirm the tenant and the licence

1. Open [https://entra.microsoft.com](https://entra.microsoft.com).
2. Check the tenant name in the toolbar. If it is not your lab tenant, switch tenants before you continue.
3. Go to `Entra ID` > `Overview` and copy the tenant ID into your password manager or a local notes file. Do not commit the tenant ID.
4. Find the licence. Useful blades are `Entra ID` > `Overview` > `Licenses`, or the Microsoft 365 admin center `Billing` > `Licenses`. Write down Free, P1, P2, or the trial name and end date.
5. Go to `Entra ID` > `Overview` > `Properties` > `Manage security defaults`. Leave the setting as it is for now. Write down whether it is enabled. You will use it later. Security defaults are the free control for legacy authentication and MFA. Details: [Security defaults](https://learn.microsoft.com/entra/fundamentals/security-defaults).

### 1.2 Create the lab group

1. Go to `Entra ID` > `Groups` > `All groups` > `New group`.
2. Use these settings:

| Field | Value |
| --- | --- |
| Group type | Security |
| Group name | `Lab Identity Users` |
| Group description | `Test users for the Entra identity lab. No production members.` |
| Membership type | Assigned |
| Members | Leave empty until the users exist |
| Roles | Do not assign a role to the group |

3. Create the group.
4. Copy the group object ID into your local notes. The Conditional Access user risk design asks for it. Do not commit it.

### 1.3 Create four lab users

Create each user with `Entra ID` > `Users` > `All users` > `New user` > `Create new user`.

| Field | Lab User 1 | Lab User 2 | Lab User 3 | Lab User 4 |
| --- | --- | --- | --- | --- |
| Display name | `Lab User 1` | `Lab User 2` | `Lab User 3` | `Lab User 4` |
| User principal name | `labuser1` | `labuser2` | `labuser3` | `labuser4` |
| Domain | Your lab tenant domain | Same | Same | Same |
| Password | You choose a unique password and store it in your password manager | Same, a different password | Same | Same |
| Auto-generate password | Off | Off | Off | Off |
| Require password change at first sign-in | Off | Off | Off | Off |
| Account enabled | Yes | Yes | Yes | Yes |
| Usage location | South Africa | South Africa | South Africa | South Africa |
| Job title | Lab | Lab | Lab | Lab |
| Department | Lab | Lab | Lab | Lab |
| Groups | `Lab Identity Users` | `Lab Identity Users` | `Lab Identity Users` | `Lab Identity Users` |
| Directory roles | None | None | None | None |

`Require password change at first sign-in` is off so the spray test has a stable password. Use this only for these lab users.

Add all four users to `Lab Identity Users` if you did not do it on the create blade. The emergency accounts must not be members of this group.

Who does what:

* Lab User 1 is the impossible travel user, the MFA fatigue user, and the user who registers an extra MFA method.
* Lab User 2 receives the low privilege role in part 7, and is included in the spray.
* Lab User 3 and Lab User 4 are spray targets only.

### 1.4 Create two emergency access accounts

Create these with the same `New user` blade. They are cloud only accounts. Prefer the `onmicrosoft.com` domain so they do not depend on a custom domain.

| Field | Emergency Access 1 | Emergency Access 2 |
| --- | --- | --- |
| Display name | `Emergency Access 1` | `Emergency Access 2` |
| User principal name | `emergency1` | `emergency2` |
| Domain | The tenant `onmicrosoft.com` domain | Same |
| Password | Long, unique, stored offline in your password manager | A different long password |
| Require password change at first sign-in | Off | Off |
| Account enabled | Yes | Yes |
| Groups | None. Do not add `Lab Identity Users` | None |
| Role | Global Administrator | Global Administrator |

Assign the role from the user blade or from `Entra ID` > `Roles & admins` > `Roles & administrators` > `Global Administrator` > `Add assignments`.

| Role setting | Value |
| --- | --- |
| Assignment type | Active |
| Permanently assigned | Yes |
| Eligible or time limited PIM assignment | Do not use this for emergency accounts |

If the portal forces Privileged Identity Management and will not allow a permanent active assignment, record that, keep the assignment active for as long as the blade allows, and say so in the report. Do not leave the emergency account as eligible only. An eligible role that you cannot activate during a lockout does not help you.

Microsoft recommends a phishing resistant sign-in method for these accounts, such as a passkey (FIDO2). A hardware key costs money. You do not need to buy one. If the tenant lets you register a passkey on your phone, or Windows Hello on your PC, do that for Emergency Access 1. Authenticator is acceptable for this lab if a passkey is not available. Use a different phone or a different method from the one your everyday admin depends on, if you can.

These two accounts are not used in the simulations.

### 1.5 Register MFA and prove the emergency accounts work

If security defaults are already on, the tenant will ask users to register MFA. If they are off, you can still register methods at [https://mysignins.microsoft.com/security-info](https://mysignins.microsoft.com/security-info) while signed in as that user. Turn security defaults on now if they are off and you do not already have Conditional Access enforcing MFA. You want MFA in place before the simulations.

Register MFA for:

* Your working admin
* Emergency Access 1
* Emergency Access 2
* Lab User 1

Leave Lab User 2, Lab User 3, and Lab User 4 without a finished MFA registration if the portal allows it. Wrong passwords during the spray never reach MFA. If the portal forces registration for them, complete it and record that. It does not ruin the spray test.

Then prove the recovery path:

1. Open a private window.
2. Sign in as Emergency Access 1 and complete MFA.
3. Confirm you can open `Entra ID` > `Users`.
4. Sign out.
5. Repeat for Emergency Access 2.
6. Sign out of the private window.
7. Return to your normal admin window and confirm it is still signed in.

**Checkpoint 3** is a sign-in log row for one emergency account, taken now, before any blocking policy exists.

## Part 2. Protect the bill, then connect logs

**Screenshot checkpoint:** 4 and 5.

### 2.1 Budget alert

1. In the Azure portal, open `Cost Management + Billing`.
2. Open the subscription that contains the Sentinel resource group.
3. Go to `Cost Management` > `Budgets`.
4. If a budget already covers this resource group, leave it. Write down the amount and the alert percent.
5. If none exists, create one at resource group scope for the Sentinel resource group. Pick an amount you are willing to notice. Alert at 50 percent and at 80 percent, to your own email.
6. A shared resource group also contains the other lab's resources. The budget watches all of it. That is what you want.

### 2.2 Daily cap

1. Open the Log Analytics workspace that Sentinel uses.
2. Go to `Usage and estimated costs` > `Daily cap`.
3. If this workspace is dedicated to a quiet lab and the cap is unset, set a cap of 1 GB per day.
4. If Sentinel-Home-SOC-Lab already ingests more than 1 GB on a normal day, do not lower the cap. Record the current value and rely on the budget alert.
5. Screenshot the cap page for checkpoint 4.

### 2.3 Confirm the table plan

1. In the same workspace, open `Tables`.
2. After the connector runs, `SigninLogs` and `AuditLogs` should be on the Analytics plan.
3. If a table is on Basic or Data lake only, switch it to Analytics for this lab, or the KQL hunts and any scheduled rule will not behave as written. Do this only for these two tables.
4. Do not extend retention. Leave the workspace retention as it already is.

### 2.4 Connect Microsoft Entra ID

You need Entra ID P1 or P2 to stream **sign-in** logs. Audit logs can stream on Entra ID Free. If the connector refuses sign-in logs and shows a licence message, screenshot that message. Continue the sign-in scenarios in the Entra portal sign-in logs, and still stream audit logs if you can. Say this clearly in the report. Do not buy P1 only to finish a screenshot unless you have decided the trial terms are acceptable.

Portal steps:

1. Azure portal > `Microsoft Sentinel` > select the home SOC workspace.
2. `Content management` > `Content hub`. Search for `Microsoft Entra ID`. If the solution is not installed, install it. Skip extra content items you do not need.
3. `Configuration` > `Data connectors` > `Microsoft Entra ID`.
4. Open the connector page.
5. Select only these two:
   * `Sign-in logs` (interactive). The table is `SigninLogs`.
   * `Audit logs`. The table is `AuditLogs`.
6. Leave these unselected: `Non-interactive user sign-in logs`, `Service principal sign-in logs`, `Managed identity sign-in logs`, provisioning logs, Microsoft Graph activity logs, and the risk tables. Risk tables need extra licence and are optional. You can turn `User risk events` and `Risky users` on later only if you have P2 and you still want that screenshot. They are not required for the four hunts.
7. Apply or connect. Use the button the page shows.
8. If the connector page has no checkboxes, use the diagnostic setting path instead:
   * Entra admin center > `Entra ID` > `Monitoring & health` > `Diagnostic settings` > `Add diagnostic setting`.
   * Diagnostic setting name: `entra-identity-lab`.
   * Logs: `SignInLogs` and `AuditLogs` only.
   * Destination: `Send to Log Analytics workspace`.
   * Subscription and workspace: the Sentinel workspace, not a new one.
   * Save.

9. Stay on the connector page until it shows a connected state for the two logs you selected. Screenshot it for checkpoint 5.

Permissions if the connect button fails: your user needs Microsoft Sentinel Contributor on the workspace, and Security Administrator (or Global Administrator) on the Entra tenant, plus permission to edit diagnostic settings.

## Part 3. Confirm one real row before any attack

**Screenshot checkpoint:** 6 and 7.

1. VPN off.
2. Private window. Sign in as Lab User 1 with the correct password. Complete MFA. Open [https://myaccount.microsoft.com](https://myaccount.microsoft.com). Sign out.
3. Wait. First time ingestion is often 15 to 60 minutes. It can be longer. Do not sign in again and again while you wait.
4. In Sentinel, open `Logs`.
5. Run:

```kusto
SigninLogs
| where TimeGenerated > ago(1d)
| project TimeGenerated, UserPrincipalName, IPAddress, Location, AppDisplayName, ResultType, ResultDescription
| take 20
```

6. You want one row for Lab User 1. Screenshot it for checkpoint 6. Then redact it using [evidence/README.md](../evidence/README.md) before you commit.
7. Run:

```kusto
AuditLogs
| where TimeGenerated > ago(1d)
| project TimeGenerated, OperationName, Category, Result
| take 20
```

8. Screenshot a real audit row for checkpoint 7. Creating the users should have produced audit events such as add user. If audit logs are connected and the table is empty, wait and run it once more. If `SigninLogs` does not exist as a table, read Troubleshooting, and use the Entra sign-in log blade for the sign-in scenarios:

`Entra ID` > `Monitoring & health` > `Sign-in logs`.

Set the date filter to the last 24 hours. Add a filter for user `Lab User 1` when you are checking one person.

## Part 4. Password spray (T1110.003)

Follow [scenarios/password-spray/simulation.md](../scenarios/password-spray/simulation.md). Exact limits, so the guide and the runbook match:

* Targets: Lab User 1, Lab User 2, Lab User 3, Lab User 4.
* Wrong passwords, typed by hand, the same three for each user: `LabWrong!A`, `LabWrong!B`, `LabWrong!C`.
* One pass only. That is 12 failed attempts. No second loop.
* Sign-in page: [https://login.microsoftonline.com](https://login.microsoftonline.com) in a private window, VPN off.
* Stop early if an account shows as locked, or if the page stops accepting attempts.
* Do not sign in as an emergency account or as your working admin on this window.

After the 12 attempts, wait for the logs, then run [scenarios/password-spray/detection.kql](../scenarios/password-spray/detection.kql). If your four users produced fewer than 6 failures or fewer than 3 distinct users in a 15 minute bin, lower `MinFailures` or `MinUsers` to match what you actually did, and write the old and new values in the report.

**Screenshot checkpoint:** 8 and 9.

Read [scenarios/password-spray/response.md](../scenarios/password-spray/response.md) and do the lab containment that applies. At minimum, confirm the lab users can still be managed from your admin session. If a lab user is locked, wait for smart lockout to clear, or reset that lab user's password from the admin blade. Microsoft has documented a default around 10 failed attempts, with a lockout that starts near one minute and then grows. Your three failures per user should stay under that default. Confirm the current values if you want, at `Protection` > `Authentication methods` > `Password protection`. Changing them needs Entra ID P1. Leave the defaults in place for this lab.

## Part 5. Impossible travel and unfamiliar location (T1078.004)

Follow [scenarios/impossible-travel/simulation.md](../scenarios/impossible-travel/simulation.md). Exact shape of the test:

* User: Lab User 1 only.
* Sign-in A: VPN off, from home, successful, MFA completed, then sign out.
* Sign-in B: VPN connected to a country that is not South Africa, successful, MFA completed, then sign out.
* Disconnect the VPN before you do any other lab work.
* Do not repeat the pair more than once if the first pair already shows two countries. If both rows show the same country, try one different VPN country a single time, then stop.

Wait for both rows, then run [scenarios/impossible-travel/detection.kql](../scenarios/impossible-travel/detection.kql).

If you have Entra ID P2, also open Identity Protection and look at the sign-in risk for Lab User 1. A VPN does not guarantee a risk detection. Screenshot whatever is true. The checklist calls this checkpoint 12.

**Screenshot checkpoint:** 10, 11, and 12.

## Part 6. MFA fatigue (T1621)

Follow [scenarios/mfa-fatigue/simulation.md](../scenarios/mfa-fatigue/simulation.md). Exact limits:

* User: Lab User 1.
* VPN off.
* Correct password.
* Deny the Authenticator prompt, or let it expire if there is no Deny button.
* Do this 5 times. Stop at 5.
* A sixth attempt that you approve is optional. If you skip it, write "not done" in the report.

Then run [scenarios/mfa-fatigue/detection.kql](../scenarios/mfa-fatigue/detection.kql). The default threshold is 4 distinct correlation IDs in 30 minutes. Five denies should clear it. If your tenant logs a different error code from `500121` or `50074`, put the code you saw into the query and record why.

**Screenshot checkpoint:** 13 and 14.

## Part 7. Suspicious changes (T1098)

Follow [scenarios/suspicious-changes/simulation.md](../scenarios/suspicious-changes/simulation.md). Exact changes:

* As Lab User 1, add one extra sign-in method at [https://mysignins.microsoft.com/security-info](https://mysignins.microsoft.com/security-info). Use a phone number you control, or Authenticator on a device you control.
* As your admin, assign Lab User 2 the directory role `Reports Reader`.
* Assignment type: Active.
* Permanent: Yes.
* Justification: `Lab evidence for T1098. Remove the same day.`
* Do not assign Global Administrator or any privileged role to a lab user.

Then run [scenarios/suspicious-changes/detection.kql](../scenarios/suspicious-changes/detection.kql).

**Screenshot checkpoint:** 15, 16, and 17.

## Part 8. Conditional Access

Read [conditional-access/README.md](../conditional-access/README.md) before you click `New policy`.

If you do not have Entra ID P1, do not deploy the JSON. Screenshot security defaults for checkpoint 18, mark checkpoint 19 as not collected, and explain the free control in the report. The designs are still part of your portfolio: they show what you would deploy when the licence exists.

If you have P1 or a P1 trial:

1. Confirm both emergency accounts can still sign in (part 1.5). If that test was not today, repeat it.
2. Keep your working admin session open.
3. Create the named locations and policies by following each markdown file in `conditional-access/`. Use the portal. You do not need a Graph app registration.
4. Every policy `Enable policy` value is `Report-only`.
5. Put both emergency access accounts in the exclude list of every policy, even though report only will not block them. You want the exclusion saved before anyone sets the policy to `On`.
6. Suggested order:
   * `LAB Home country` named location
   * `LAB DESIGN: Block legacy authentication`
   * `LAB DESIGN: Require multifactor authentication`
   * `LAB DESIGN: Require MFA outside home country`
   * `LAB DESIGN: Block security info registration outside home country`
   * P2 only, and only if you choose to: the two risk policies
7. Generate one normal sign-in as Lab User 1.
8. Open that sign-in log > `Conditional Access` tab. You want the lab policies to show a report only result. Screenshot it for checkpoint 19.
9. Leave the policies in `Report-only` or set them back to `Off` in cleanup. Do not leave a block policy set to `On`.

**Screenshot checkpoint:** 18 and 19.

## Part 9. Write the report

1. Copy [report-template.md](report-template.md) to a file you will commit, for example `docs/lab-report.md`, after it contains your real notes.
2. Fill every placeholder from your screenshots and query output.
3. Where a step was skipped because of licence or because you chose not to take a trial, say that in one sentence. Do not describe a control as tested if you only designed it.
4. Redact screenshots and commit them under `evidence/` using the checklist names.

The report is yours to write. The template is a structure, not a filled example.

## Part 10. Cleanup

Do this even if you plan to polish the report tomorrow. Cleanup of access is separate from keeping screenshots.

1. Remove `Reports Reader` from Lab User 2. Confirm the audit log later shows a remove event if you want that in the report. Checkpoint 15 can stay as the add event you already captured.
2. Remove the extra MFA method from Lab User 1. Leave the original method in place until you are ready to disable the user.
3. Set every policy whose name starts with `LAB DESIGN` to `Off`, or delete those policies. Delete the `LAB Home country` and `LAB Home IP` named locations if you created them and you do not want them left behind.
4. If you turned security defaults off and you are not keeping an enforced, tested Conditional Access baseline, turn security defaults back on.
5. Disable analytics rules whose names start with `LAB`, if you created any. Set them to `Disabled`.
6. When you no longer need new log rows for the report, open the Entra ID data connector and clear `Sign-in logs` and `Audit logs`, or remove the `entra-identity-lab` diagnostic setting. Do this when the report is done, not before the screenshots exist.
7. Disconnect the VPN if it is still on.
8. Disable sign-in for Lab User 1 to Lab User 4, or delete those four users. Deleting is fine in a lab tenant you own. Keep the emergency access accounts while any Conditional Access policy remains in the tenant. If security defaults are back on and all lab policies are gone, you may delete the emergency accounts too, as long as your working admin can still sign in. If you are unsure, keep the emergency accounts and record that.
9. Do not delete the Sentinel workspace, the Log Analytics workspace, or the resource group.
10. The next day, open Cost Management and confirm the budget alert is still in place. Screenshot cleanup for checkpoint 20.

## Optional: save a hunt as an analytics rule

Do this only after the hunt returns your lab rows, and only if you want the extra screenshot. A rule with no real trigger is not evidence of detection.

1. Sentinel > `Configuration` > `Analytics` > `Create` > `Scheduled query rule`.
2. Name: `LAB Password spray failures` (or the matching scenario name).
3. Severity: `Medium` for the spray and MFA hunts. `High` for the suspicious change hunt.
4. Query: paste the scenario query.
5. Entity mapping: `Account` with `Name` = `UserPrincipalName` where that column exists. `IP` with `Address` = `IPAddress` where that column exists. The audit query uses `TargetUpn` and `ActorIp`. Map those if you build that rule.
6. Query scheduling: run every 1 hour, look back 1 day.
7. Alert threshold: greater than 0.
8. Group the events into one alert by the account entity where the UI offers it.
9. Query suppression or alert grouping: if you see a suppression window, 6 hours is enough for a lab.
10. Automated response: none.
11. After you have the screenshot you want, disable the rule in part 10. A rule left enabled is harmless only while data and thresholds stay quiet. Disable it anyway.

## Troubleshooting

**The connector says sign-in logs need P1 or P2.**
Expected on Entra ID Free. Stream audit logs if that checkbox works. Do the three sign-in scenarios in `Entra ID` > `Monitoring & health` > `Sign-in logs`. Say in the report that KQL on `SigninLogs` was not available on this licence. Keep the `.kql` files. They are the detection you would deploy with P1.

**`SigninLogs` is missing, or the query returns no rows, and you do have P1.**
Check all of these before you generate more sign-ins:

* The Sentinel workspace in the query window is the one you connected.
* The connector shows `Sign-in logs` connected, or the diagnostic setting `entra-identity-lab` includes `SignInLogs` and this workspace.
* You are inside the time range. `ago(1d)` does not show yesterday if the workspace timezone filter is wrong. Start with `ago(7d)`.
* You waited. Refresh once after 30 minutes, not every minute.
* Another diagnostic setting is not the problem by itself, but a setting pointed at a different workspace would explain empty logs here. Open `Diagnostic settings` and read the destination.

**The spray query returns no rows but the portal shows the failures.**
Lower `MinUsers` and `MinFailures`. Confirm `ResultType` in the portal. If the code is not `50126`, `50053`, `50055`, or `50056`, add the code you see. Error `50126` means the username or password was invalid.

**The travel query returns no rows.**
Open the two sign-in rows and read `Location`. If the country is blank, the query drops the row. If both countries match, the VPN exit was still geolocated to your country. One more exit country is the limit. If the sign-ins were more than 120 minutes apart, raise `MaxMinutes` to the gap you actually have and record the change.

**Identity Protection shows nothing.**
Normal without P2, and also possible with P2. Microsoft's atypical travel and unfamiliar sign-in detections are premium, and a single VPN hop is not guaranteed to score. Checkpoint 12 can be a picture of an empty or unavailable blade.

**MFA never prompts.**
Security defaults may be off and no Conditional Access policy requires MFA. Turn security defaults on, register Lab User 1, and try again. Do not build a custom attack tool to force prompts.

**You cannot enable a Conditional Access policy.**
Security defaults are probably still on. That is fine. Leave them on unless you are ready to replace them in the same session.

**A lab user is locked.**
Stop the spray. From the admin session, open the user and reset the password, or wait for the lockout to expire. Do not delete the tenant.

**You are locked out of the admin account.**
Use a private window and Emergency Access 1 or Emergency Access 2. If a policy you set to `On` is the cause, set that policy to `Off` with the emergency account. If you have no working admin and no working emergency account, use the account recovery process for the Microsoft 365 or Azure subscription that owns the tenant. This is why part 1 comes first.

**Charges after the lab.**
Clear the connector checkboxes, confirm you did not create a virtual machine, and read Cost Management at resource group scope. Delete lab users and lab policies. Leave the shared Sentinel resources in place.

## Official references

* [Send Entra ID data to Microsoft Sentinel](https://learn.microsoft.com/azure/sentinel/connect-azure-active-directory)
* [Sentinel billing and the 31 day trial](https://learn.microsoft.com/azure/sentinel/billing)
* [Security defaults](https://learn.microsoft.com/entra/fundamentals/security-defaults)
* [Emergency access accounts](https://learn.microsoft.com/entra/identity/role-based-access-control/security-emergency-access)
* [Block legacy authentication](https://learn.microsoft.com/entra/identity/conditional-access/policy-block-legacy-authentication)
* [Sign-in error codes](https://learn.microsoft.com/entra/identity/monitoring-health/howto-troubleshoot-sign-in-errors)
* [Risk detections](https://learn.microsoft.com/entra/id-protection/concept-identity-protection-risks)
* [Audit activity reference](https://learn.microsoft.com/entra/identity/monitoring-health/reference-audit-activities)
