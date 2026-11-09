# Named locations

**Licence:** Microsoft Entra ID P1 to use a named location in a Conditional Access policy.

**JSON:** [named-location-home-country.json](named-location-home-country.json) and [named-location-home-ip.json](named-location-home-ip.json)

Create the country location. Create the IP location only if you want the extra precision and you accept that a home IP can change.

Do not commit your public IP or the named location object ID.

## Country location

`Entra ID` > `Conditional Access` > `Named locations` > `Countries location`.

| Setting | Value |
| --- | --- |
| Name | `LAB Home country` |
| Countries | South Africa. Change this if you are not signing in from South Africa. The JSON uses `ZA` as that example. |
| Include unknown countries and regions | No |

Unknown countries should stay excluded from the "home" definition. An IP Microsoft cannot place should not count as Cape Town.

## IP location (optional)

Check your current public IPv4 on your router, or on a lookup site you trust. Do not use the VPN. The VPN must be off, or you will store the VPN address as home.

`Named locations` > `IP ranges location`.

| Setting | Value |
| --- | --- |
| Name | `LAB Home IP` |
| Mark as trusted location | No |
| Range | Your current public IPv4, as a `/32` |
| IPv6 | If you only have IPv6, add that prefix instead. The Graph type is `#microsoft.graph.iPv6CidrRange`. The sample JSON is IPv4. |

Leave trusted off. Trusted named locations are treated more kindly by some risk detections. A wrong trusted range would weaken the tenant.

If the address changes daily, skip the IP location and use the country only.

## Where these are used

* [Require MFA outside the home country](require-mfa-outside-home-country.md) excludes `LAB Home country`.
* [Block security info registration outside the home country](block-security-info-registration-outside-home.md) excludes `LAB Home country`.

Neither policy uses the IP location unless you edit it to do so. Country is the stable lab choice.
