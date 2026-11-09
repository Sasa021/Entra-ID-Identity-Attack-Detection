# Lab report: Entra ID identity attack detection

Write this report after the lab, in your own words. Delete each `[PLACEHOLDER]` by replacing it with what you observed. If you did not do a step, write `Not done` and the reason. Do not paste example numbers from the lab guide. The guide has limits, not results.

Copy this file to `docs/lab-report.md` when you are ready to commit it. You can keep this template as it is.

## Document control

| Field | Value |
| --- | --- |
| Author | Mosa Mofokeng |
| Date of the lab | `[PLACEHOLDER]` |
| Tenant type | `[PLACEHOLDER: for example personal lab tenant, trial, or developer sandbox]` |
| Licence observed | `[PLACEHOLDER: Free, P1, P2, or trial name and end date]` |
| Sentinel workspace | `[PLACEHOLDER: workspace name only if you are comfortable publishing it]` |
| Sentinel free trial dates | `[PLACEHOLDER: dates shown in the portal, or not shown]` |
| Daily cap or budget | `[PLACEHOLDER]` |
| Evidence redacted | `[PLACEHOLDER: yes, and the date you reviewed the images]` |

## 1. Summary

`[PLACEHOLDER: five to eight sentences. What you simulated, which logs you had, which hunts returned rows, and which controls you deployed or only designed. No metrics you did not read off a screen.]`

## 2. Scope

This lab used test users in a tenant I control. I did not target any other organisation.

| Item | What I used |
| --- | --- |
| Lab users | `[PLACEHOLDER]` |
| Emergency access accounts created | `[PLACEHOLDER: 0, 1, or 2, and the MFA method type, not the phone number]` |
| VPN provider and exit country | `[PLACEHOLDER]` |
| Scenarios completed | `[PLACEHOLDER]` |
| Scenarios skipped | `[PLACEHOLDER]` |

## 3. Environment

`[PLACEHOLDER: how you connected Entra ID to the Sentinel workspace from Sentinel-Home-SOC-Lab. Say whether SigninLogs and AuditLogs both received data. If sign-in streaming was blocked by licence, say that here.]`

Architecture I used:

`[PLACEHOLDER: one short paragraph. You can refer to the diagram in the README and note anything that differed, such as portal sign-in logs instead of SigninLogs.]`

## 4. Password spray (T1110.003)

| Item | Record |
| --- | --- |
| Method | `[PLACEHOLDER: confirm the four users, the three wrong passwords, and that you did one pass]` |
| Where I looked first | `[PLACEHOLDER: Entra sign-in logs, Sentinel, or both]` |
| Error codes I actually saw | `[PLACEHOLDER]` |
| Query used | `scenarios/password-spray/detection.kql` |
| Threshold changes | `[PLACEHOLDER: none, or the old and new MinUsers and MinFailures]` |
| Rows returned | `[PLACEHOLDER]` |
| Screenshots | `evidence/08-password-spray-failures.png`, `evidence/09-password-spray-kql.png` |
| Containment I performed | `[PLACEHOLDER]` |
| What I would tell a SOC lead | `[PLACEHOLDER]` |

## 5. Impossible travel and unfamiliar location (T1078.004)

| Item | Record |
| --- | --- |
| Home location shown in the log | `[PLACEHOLDER: city and country only]` |
| VPN location shown in the log | `[PLACEHOLDER: city and country only]` |
| Minutes between the two successes | `[PLACEHOLDER: from the log timestamps]` |
| Query rows returned | `[PLACEHOLDER]` |
| Threshold changes | `[PLACEHOLDER]` |
| Identity Protection result | `[PLACEHOLDER: detection name, risk none, or not licensed. Attach evidence/12]` |
| Screenshots | `evidence/10-impossible-travel-two-locations.png`, `evidence/11-impossible-travel-kql.png`, `evidence/12-identity-protection-or-empty.png` |
| Containment I performed | `[PLACEHOLDER]` |

## 6. MFA fatigue (T1621)

| Item | Record |
| --- | --- |
| Prompts I denied or allowed to expire | `[PLACEHOLDER: the number I actually did, maximum 5]` |
| Optional approved attempt | `[PLACEHOLDER: done or not done]` |
| Error codes I saw | `[PLACEHOLDER]` |
| Query attempt count | `[PLACEHOLDER]` |
| Screenshots | `evidence/13-mfa-fatigue-failures.png`, `evidence/14-mfa-fatigue-kql.png` |
| Containment I performed | `[PLACEHOLDER]` |

## 7. Suspicious changes (T1098)

| Item | Record |
| --- | --- |
| MFA method added | `[PLACEHOLDER: type of method, such as phone or authenticator. No number.]` |
| Audit operation name for that change | `[PLACEHOLDER]` |
| Role assigned | `[PLACEHOLDER]` |
| Assignment active or eligible | `[PLACEHOLDER]` |
| Audit operation name for the role | `[PLACEHOLDER]` |
| Query rows that matched these two changes | `[PLACEHOLDER]` |
| Screenshots | `evidence/15-mfa-method-registered-audit.png`, `evidence/16-role-assignment-audit.png`, `evidence/17-suspicious-change-kql.png` |
| How I removed both changes | `[PLACEHOLDER]` |

## 8. Conditional Access

| Design | Licence it needs | What I did |
| --- | --- | --- |
| Security defaults | Entra ID Free | `[PLACEHOLDER: left on, turned off, or turned back on]` |
| Block legacy authentication | P1 | `[PLACEHOLDER: design only, report only, or off]` |
| Require MFA | P1 | `[PLACEHOLDER]` |
| Named locations | P1 | `[PLACEHOLDER]` |
| MFA outside home country | P1 | `[PLACEHOLDER]` |
| Block security info registration outside home country | P1 | `[PLACEHOLDER]` |
| Sign-in risk MFA | P2 | `[PLACEHOLDER]` |
| User risk password change | P2 | `[PLACEHOLDER]` |

Report only evidence: `[PLACEHOLDER: evidence/19 file name, or Not collected]`

`[PLACEHOLDER: two or three sentences on lockout risk and how the emergency accounts were excluded or, if you stayed on security defaults, why exclusion was not available.]`

## 9. Detection quality

`[PLACEHOLDER: for each hunt, say whether the default threshold matched the lab, what would be noisy in a larger tenant, and what you would not alert on.]`

Optional analytics rule: `[PLACEHOLDER: not created, or the rule name and that you disabled it]`

## 10. Cost and cleanup

| Check | Record |
| --- | --- |
| Budget or daily cap left in place | `[PLACEHOLDER]` |
| Connector checkboxes cleared, and the date | `[PLACEHOLDER: or still on, and why]` |
| Lab users disabled or deleted | `[PLACEHOLDER]` |
| Extra MFA method removed | `[PLACEHOLDER]` |
| Reports Reader removed | `[PLACEHOLDER]` |
| Lab policies off or deleted | `[PLACEHOLDER]` |
| VPN disconnected | `[PLACEHOLDER]` |
| Resource group deleted | No. The Sentinel resource group stays for Sentinel-Home-SOC-Lab. |
| Cost Management check the next day | `[PLACEHOLDER: what you saw, in your own words, without inventing a figure. You can write the figure the portal showed.]` |

## 11. Lessons learned

* What was harder than I expected: `[PLACEHOLDER]`
* What I had to tune: `[PLACEHOLDER]`
* Which control I would turn on first with the licence I had, and why: `[PLACEHOLDER]`
* What I would change on a second run: `[PLACEHOLDER]`
* What this lab does not prove: `[PLACEHOLDER: for example, no P2 risk detections, or no enforced Conditional Access]`

## 12. Evidence index

List every file you committed under `evidence/`. Note any checklist number you skipped.

| Checklist number | File | In the report |
| --- | --- | --- |
| 1 | `[PLACEHOLDER]` | `[PLACEHOLDER]` |
| 2 | `[PLACEHOLDER]` | `[PLACEHOLDER]` |
| 3 | `[PLACEHOLDER]` | `[PLACEHOLDER]` |
| 4 | `[PLACEHOLDER]` | `[PLACEHOLDER]` |
| 5 | `[PLACEHOLDER]` | `[PLACEHOLDER]` |
| 6 | `[PLACEHOLDER]` | `[PLACEHOLDER]` |
| 7 | `[PLACEHOLDER]` | `[PLACEHOLDER]` |
| 8 | `[PLACEHOLDER]` | `[PLACEHOLDER]` |
| 9 | `[PLACEHOLDER]` | `[PLACEHOLDER]` |
| 10 | `[PLACEHOLDER]` | `[PLACEHOLDER]` |
| 11 | `[PLACEHOLDER]` | `[PLACEHOLDER]` |
| 12 | `[PLACEHOLDER]` | `[PLACEHOLDER]` |
| 13 | `[PLACEHOLDER]` | `[PLACEHOLDER]` |
| 14 | `[PLACEHOLDER]` | `[PLACEHOLDER]` |
| 15 | `[PLACEHOLDER]` | `[PLACEHOLDER]` |
| 16 | `[PLACEHOLDER]` | `[PLACEHOLDER]` |
| 17 | `[PLACEHOLDER]` | `[PLACEHOLDER]` |
| 18 | `[PLACEHOLDER]` | `[PLACEHOLDER]` |
| 19 | `[PLACEHOLDER]` | `[PLACEHOLDER]` |
| 20 | `[PLACEHOLDER]` | `[PLACEHOLDER]` |
