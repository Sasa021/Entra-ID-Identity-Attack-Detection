# Block legacy authentication

**Licence:** Microsoft Entra ID P1. Without P1, security defaults already block legacy authentication. Use that, and leave this file as a design.

**JSON:** [block-legacy-authentication.json](block-legacy-authentication.json)

**Attack this relates to:** password spray ([T1110.003](https://attack.mitre.org/techniques/T1110/003/)). Legacy clients such as IMAP, POP, and SMTP AUTH cannot complete MFA, so a correct password is enough.

Official portal guide: [Block legacy authentication with Conditional Access](https://learn.microsoft.com/entra/identity/conditional-access/policy-block-legacy-authentication).

## Portal settings

`Entra ID` > `Conditional Access` > `Policies` > `New policy`.

| Setting | Value |
| --- | --- |
| Name | `LAB DESIGN: Block legacy authentication` |
| Users > Include | All users |
| Users > Exclude | Emergency Access 1 and Emergency Access 2 |
| Target resources > Include | All resources (the portal may still say all cloud apps) |
| Conditions > Client apps > Configure | Yes |
| Client apps selected | Exchange ActiveSync clients, and Other clients |
| Client apps not selected | Browser, Mobile apps and desktop clients |
| Grant | Block access |
| Enable policy | `Report-only` |

Leave session controls unset.

Browser sign-ins are out of scope for this policy on purpose. Your lab scenarios use the browser. This policy is about older mail protocols.

## How you will know it evaluated

After a sign-in, open the sign-in log > Conditional Access. This policy should show a report only result. A browser sign-in may show `notApplied` because the client app does not match. That is a successful test of the scope. Record the result word the portal uses.

You do not need to enable IMAP on a mailbox to prove the design. Turning on a legacy protocol just to watch it fail is optional, and it is easy to forget it enabled. Skip that extra test unless you already have a lab mailbox and you will turn the protocol off again the same day.

## Before you ever set it to On

* Both emergency accounts are in the exclude list.
* You have seen a report only row.
* Security defaults are off, because the two features do not run together. If security defaults are still on, you are already blocking legacy authentication and you can leave this policy undeployed.
