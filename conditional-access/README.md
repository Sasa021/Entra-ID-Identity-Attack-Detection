# Conditional Access designs

These files are designs for your lab tenant. They are not a record of policies you have already turned on. The JSON uses placeholder IDs so it cannot be pasted into Graph and succeed until you replace those IDs on purpose.

Deploy from the portal steps in each markdown file, in `Report-only`, and only if the licence allows it. `Report-only` evaluates the policy and writes the result on the sign-in log. It does not block the sign-in.

The lab guide part 8 is the order of operations. Read that before you create anything.

## What each design is for

| Design | Attack it makes harder | Licence |
| --- | --- | --- |
| [Block legacy authentication](block-legacy-authentication.md) | Password spray over IMAP, POP, SMTP, and other clients that cannot do MFA ([T1110.003](https://attack.mitre.org/techniques/T1110/003/)) | Entra ID P1 |
| [Require MFA](require-mfa.md) | A correct password becoming a session, including spray and stolen passwords ([T1078.004](https://attack.mitre.org/techniques/T1078/004/)) | Entra ID P1 |
| [Home country and home IP](named-locations.md) | Location conditions used by the next two designs | Entra ID P1 |
| [Require MFA outside the home country](require-mfa-outside-home-country.md) | A sign-in from a country you did not name ([T1078.004](https://attack.mitre.org/techniques/T1078/004/)) | Entra ID P1 |
| [Block security info registration outside the home country](block-security-info-registration-outside-home.md) | An attacker registering a new MFA method from somewhere else ([T1098](https://attack.mitre.org/techniques/T1098/)) | Entra ID P1 |
| [MFA on medium or high sign-in risk](sign-in-risk.md) | Risky sign-ins, including atypical travel when Identity Protection scores them | Entra ID P2 |
| [Password change on high user risk](user-risk.md) | A user Identity Protection already believes is compromised | Entra ID P2 |

MFA fatigue ([T1621](https://attack.mitre.org/techniques/T1621/)) still needs the MFA prompt. A require MFA policy creates that prompt. Number matching in Microsoft Authenticator, and the hunt on denied prompts, are what make a flood of prompts visible and harder to approve by mistake. Number matching is described in the MFA fatigue response notes.

Assign a P1 licence to each user included in a P1 policy, and a P2 licence to each user included in a risk policy, before you move that policy from `Report-only` to `On`. Include your working admin if the policy includes all users. Excluded emergency accounts do not need a licence for a policy that skips them. If the trial does not contain enough seats, narrow the include list to `Lab Identity Users` plus your admin, and write that change in the report.

## Free alternative: security defaults

If you do not have Entra ID P1, use security defaults. They are included with Entra ID Free.

Security defaults, when enabled:

* Block legacy authentication, including Exchange ActiveSync basic authentication.
* Ask users to register MFA.
* Require MFA for administrator roles, and require MFA for users when Microsoft judges the sign-in needs it.

Security defaults do not let you exclude the emergency access accounts, pick named locations, or use risk levels. You cannot turn individual pieces off.

Conditional Access policies will not be enforced while security defaults are on. Path to the toggle: `Entra ID` > `Overview` > `Properties` > `Manage security defaults`.

If you turn security defaults off, you have removed that free baseline. In the same session, with an admin window left open, create at least the require MFA policy and the block legacy policy, both in `Report-only`, and test a sign-in. Put them to `On` only after the report only results look right and both emergency accounts are excluded. If you are not ready to do that, leave security defaults on and keep these files as designs.

Official reference: [Security defaults](https://learn.microsoft.com/entra/fundamentals/security-defaults).

## Lockout rules

* Create two emergency access accounts first and sign in as both. The lab guide part 1 is that work. Microsoft's guidance: [emergency access accounts](https://learn.microsoft.com/entra/identity/role-based-access-control/security-emergency-access).
* Exclude both emergency accounts from every policy before you ever set a policy to `On`. Report only does not need the exclusion to keep you safe, and you still store the exclusion now.
* A hardware FIDO2 key costs money. You do not need to buy one. A passkey on a phone or Windows Hello is enough if the tenant offers it. Authenticator is acceptable for this lab if it is not the only method standing between you and lockout.
* Keep one browser signed in as the working admin while you edit policies. Test in a private window.
* Do not set `Block access` and `All users` and `All cloud apps` with client apps set to all. The legacy design blocks only legacy clients. The security info design blocks only the registration action, and only outside the home country, and it should stay in report only during the lab.
* Do not mark your home IP as a trusted location in this lab. A trusted location changes risk calculations. Leave `isTrusted` false until you have a stable address and a reason.
* ISP addresses change. A single `/32` can be wrong tomorrow. Prefer the country named location for the lab. The IP named location is optional.
* After the lab, set every `LAB DESIGN` policy to `Off` or delete it, unless you have decided to keep a tested baseline. If you do not keep that baseline, turn security defaults back on.

## Cost

Conditional Access has no virtual machine and no per query fee. The costs that still exist:

* A P1 or P2 licence trial can bill you when it ends. Read the offer. Cancel it in Microsoft 365 admin center > `Billing` > `Your products` before the end date if you only needed it for this lab.
* Sign-in logs in Sentinel are a separate bill, covered in the lab guide. Creating policies does not, by itself, increase that ingestion.
* Do not create a Graph application registration just to upload these JSON files. The portal path avoids a client secret.

## How to read the JSON

* `state` is `enabledForReportingButNotEnforced`. In the portal that is `Report-only`.
* `REPLACE_WITH_...` values are not real object IDs. Graph will reject them. That is deliberate.
* Replace them only on a copy, on your machine, if you choose to call Graph. Do not commit the real object IDs.
* Portal clicks are the supported path for this lab.
