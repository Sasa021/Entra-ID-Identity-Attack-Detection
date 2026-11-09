# Require multifactor authentication

**Licence:** Microsoft Entra ID P1. Without P1, security defaults cover a simpler version of this control.

**JSON:** [require-mfa.json](require-mfa.json)

**Attack this relates to:** stolen or sprayed passwords ([T1110.003](https://attack.mitre.org/techniques/T1110/003/), [T1078.004](https://attack.mitre.org/techniques/T1078/004/)). MFA is also what makes [T1621](https://attack.mitre.org/techniques/T1621/) possible: the attacker has the password and starts prompting. You still want MFA. Pair it with number matching and the fatigue hunt.

## Portal settings

`Entra ID` > `Conditional Access` > `Policies` > `New policy`.

| Setting | Value |
| --- | --- |
| Name | `LAB DESIGN: Require multifactor authentication` |
| Users > Include | All users |
| Users > Exclude | Emergency Access 1 and Emergency Access 2 |
| Target resources > Include | All resources |
| Conditions > Client apps > Configure | Yes |
| Client apps selected | Browser, Mobile apps and desktop clients |
| Client apps not selected | Exchange ActiveSync clients, Other clients |
| Grant | Require multifactor authentication |
| For multiple controls | Require one of the selected controls. The JSON operator is `OR`. There is only one control. |
| Enable policy | `Report-only` |

Legacy clients are handled by the block legacy design. If you set this policy's client apps to all, legacy clients fail MFA and the effect is a block. Keep the split so each policy has one job.

## How you will know it evaluated

Sign in as Lab User 1 in a private window and complete MFA. On that sign-in log, this policy should show a report only result. It should not have stopped the sign-in.

If the result says the policy did not apply, check that security defaults are off and that this policy is `Report-only` rather than `Off`.

## Before you ever set it to On

* You can still pass MFA as your working admin and as Lab User 1.
* Emergency accounts are excluded, and you have signed in as each of them since the exclusion was saved.
* Users who cannot pass MFA yet are not in the include scope, or you accept that they will be prompted. In this lab the include scope is all users, so check Lab User 2, 3, and 4 before you enforce it. Leave the policy in report only if those three have not registered MFA.
