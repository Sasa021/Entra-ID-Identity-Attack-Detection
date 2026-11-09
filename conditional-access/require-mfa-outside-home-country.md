# Require MFA outside the home country

**Licence:** Microsoft Entra ID P1.

**Needs:** the `LAB Home country` named location in [named-locations.md](named-locations.md).

**JSON:** [require-mfa-outside-home-country.json](require-mfa-outside-home-country.json)

**Attack this relates to:** a valid account used from a country you did not plan for ([T1078.004](https://attack.mitre.org/techniques/T1078/004/)). This is the control that pairs with the impossible travel hunt. The hunt detects. This policy adds MFA when the sign-in is outside South Africa (or the country you configured).

This design requires MFA. It does not block the foreign sign-in. A block would lock you out the moment you travel or the moment geolocation is wrong. Do not convert it to block for this lab.

## Portal settings

Create `LAB Home country` first. Then `New policy`.

| Setting | Value |
| --- | --- |
| Name | `LAB DESIGN: Require MFA outside home country` |
| Users > Include | All users |
| Users > Exclude | Emergency Access 1 and Emergency Access 2 |
| Target resources > Include | All resources |
| Conditions > Location > Configure | Yes |
| Include | Any location |
| Exclude | `LAB Home country` |
| Client apps | Leave at the default the portal shows. If you configure them, select Browser and Mobile apps and desktop clients. |
| Grant | Require multifactor authentication |
| Enable policy | `Report-only` |

## How you will know it evaluated

* A sign-in from home should not match, because the country is excluded. The policy result is typically not applied. Record the word you see.
* The VPN sign-in from part 5, if you still have that row, or one new sign-in while the VPN is on, should match and show a report only MFA result. Disconnect the VPN afterwards.

If home geolocation is not South Africa, fix the named location before you trust the result. Do not set the policy to `On` to "see what happens".

## Geolocation can be wrong

Mobile carriers and some home ISPs are placed in a neighbouring country, or in "unknown". With unknown countries left out of `LAB Home country`, an unknown IP matches this policy and would require MFA once the policy is on. That is acceptable only if you can pass MFA. It is another reason to stay in report only until you have read a few real sign-ins.
