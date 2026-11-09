# Block security info registration outside the home country

**Licence:** Microsoft Entra ID P1.

**Needs:** `LAB Home country` from [named-locations.md](named-locations.md).

**JSON:** [block-security-info-registration-outside-home.json](block-security-info-registration-outside-home.json)

**Attack this relates to:** [T1098](https://attack.mitre.org/techniques/T1098/). After a password is known, a common next step is to register a new MFA method. This policy is aimed at that registration action, not at ordinary sign-in.

The user action in Graph is `urn:user:registersecurityinfo`.

## Warning

The grant is block, and the state in the file is still report only. If you set this policy to `On` while your IP is outside `LAB Home country`, you can stop yourself from registering or updating MFA. Leave it in `Report-only` for the lab.

Do not create this policy until Lab User 1 has already registered the MFA method used in the scenarios. Registration for the lab happens in part 1 and part 7, before you depend on this control.

## Portal settings

`Entra ID` > `Conditional Access` > `Policies` > `New policy`.

| Setting | Value |
| --- | --- |
| Name | `LAB DESIGN: Block security info registration outside home country` |
| Users > Include | All users |
| Users > Exclude | Emergency Access 1 and Emergency Access 2 |
| Target resources > User actions | Register security information |
| Target resources > Cloud apps | Do not also select all resources. This policy targets the user action. |
| Conditions > Location > Configure | Yes |
| Include | Any location |
| Exclude | `LAB Home country` |
| Grant | Block access |
| Enable policy | `Report-only` |

If the portal does not show client apps for a user action policy, leave them unset. The JSON includes `"clientAppTypes": ["all"]` because Graph often expects it. If a Graph call rejects that property, remove it and submit the rest. The portal path does not need that experiment.

## How you will know it evaluated

Open security info while signed in as Lab User 1 from home, after the policy exists in report only. Then open the sign-in or audit detail and look for this policy name. From home, location exclude should mean the policy does not apply. Record the actual result. Do not turn the policy on and then try the registration from the VPN.
