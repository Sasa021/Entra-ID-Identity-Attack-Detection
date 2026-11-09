# Require MFA for medium or high sign-in risk

**Licence:** Microsoft Entra ID P2. This includes Identity Protection. Entra ID P1 cannot use sign-in risk as a condition. If you do not have P2, do not start a trial for this one policy unless you have read the billing terms and written down the end date. The impossible travel KQL hunt is the free detection.

**JSON:** [sign-in-risk.json](sign-in-risk.json)

**Attack this relates to:** sign-ins Identity Protection scores as risky, which can include atypical travel (`unlikelyTravel`), unfamiliar sign-in properties, and anonymous IP addresses. The product may not score your VPN test. A report only sign-in can show this policy as not applied when the sign-in risk is blank or none. Record that result. The JSON is still a valid design.

## Portal settings

`Entra ID` > `Conditional Access` > `Policies` > `New policy`.

| Setting | Value |
| --- | --- |
| Name | `LAB DESIGN: Require MFA for medium or high sign-in risk` |
| Users > Include | All users |
| Users > Exclude | Emergency Access 1 and Emergency Access 2 |
| Target resources > Include | All resources |
| Conditions > Sign-in risk > Configure | Yes |
| Sign-in risk levels | High and Medium. Leave Low unselected so ordinary noise does not match. |
| Grant | Require multifactor authentication |
| Enable policy | `Report-only` |

If the sign-in risk condition is missing or greyed out, you do not have P2 on this tenant. Stop. Screenshot the greyed control or the licence message if one is shown, and mark the policy as design only in the report.

## Scope note

The JSON includes all users, with the emergency accounts excluded. High sign-in risk on your working admin would demand MFA at sign-in once the policy is enforced. Report only will not do that. Do not enforce it during the lab.
