# Require password change for high user risk

**Licence:** Microsoft Entra ID P2.

**JSON:** [user-risk.json](user-risk.json)

**Attack this relates to:** a user Identity Protection has marked as high risk, for example after leaked credentials or a successful premium password spray detection. This is a response control. It is not how you detect the four lab scenarios.

## Why the lab scope is the lab group

The other designs include all users because they describe a tenant baseline. This one can force a password change. The JSON and the portal steps below include only the `Lab Identity Users` group, so a mistaken switch to `On` does not force your working admin to reset their password.

Even then, leave it in `Report-only`. Lab User 1 is in that group. An enforced password change in the middle of the lab is annoying, and it is dangerous if you do not have the new password stored.

## Portal settings

| Setting | Value |
| --- | --- |
| Name | `LAB DESIGN: Password change for high user risk` |
| Users > Include | `Lab Identity Users` |
| Users > Exclude | Emergency Access 1 and Emergency Access 2, even though they are not in the group |
| Target resources > Include | All resources |
| Conditions > User risk > Configure | Yes |
| User risk levels | High only |
| Grant | Require multifactor authentication, and require password change |
| For multiple controls | Require all the selected controls. The JSON operator is `AND`. |
| Enable policy | `Report-only` |

Password change as a grant control needs the combined registration experience, and the user must pass MFA as well. Microsoft documents that pairing. If the portal refuses one of the grant checkboxes, leave the policy undeployed and describe the message in the report.

If user risk is greyed out, you do not have P2. Do not deploy it.

## What a report only test can show

It can show the policy exists and that a normal lab sign-in did not match, because the user was not high risk. It cannot show a password change unless Identity Protection has actually marked a lab user high risk. Do not mark a user compromised by hand just to fire this policy, unless you are ready to remediate that user immediately and you say you did it on purpose. Skipping that is the better lab choice.
