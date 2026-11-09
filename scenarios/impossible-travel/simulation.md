# Impossible travel and unfamiliar location (T1078.004)

You sign in as Lab User 1 from home, then once through a VPN in another country. Both sign-ins are you. The log should show two places that you could not have travelled between in that time.

The attacker technique is [T1078.004 Valid Accounts: Cloud Accounts](https://attack.mitre.org/techniques/T1078/004/): a valid cloud account is used from a place that does not match the person's normal sign-ins. Impossible travel and unfamiliar location are detection ideas for that behaviour. They are not separate ATT&CK technique IDs.

* Impossible travel: two sign-ins, too far apart for the time between them.
* Unfamiliar location: a country or network that this account has not used before. A brand new lab user has almost no history, so "unfamiliar" is a weak claim until the account has signed in for a while. The country change is the evidence you can show on day one.

## Stay inside this tenant

* Only Lab User 1, in your tenant.
* Only a VPN provider you trust. Proton VPN's free plan is enough if you do not already have a VPN. It offers a few exit countries.
* Connect the VPN only for sign-in B, then disconnect.
* Do not use Tor, a public proxy list, a compromised host, or an Azure virtual machine. A virtual machine in another region would bill you.
* Do not send personal banking or private mail through the VPN session.
* A free VPN provider can see the traffic that passes through it. Limit that traffic to the Microsoft sign-in.

## Before you start

* Lab User 1 can already complete MFA. Part 1 of the lab guide covers registration.
* You know which city the first sign-in is likely to show. For this project that is Cape Town, or whichever South African city your ISP geolocates to. The log is the authority, not the map in your head.
* VPN is off.
* You have a private window for Lab User 1 and a separate admin window.

## Steps

1. Write down the start time.
2. Private window, VPN off. Sign in as Lab User 1 at [https://login.microsoftonline.com](https://login.microsoftonline.com) with the real lab password.
3. Complete MFA.
4. Open [https://myaccount.microsoft.com](https://myaccount.microsoft.com) so the sign-in finishes. Sign out. Close the window.
5. Turn the VPN on. In the VPN app, pick an exit country that is not South Africa. Leave the app's country label in your notes.
6. Confirm the VPN is connected before the next sign-in.
7. New private window. Sign in as Lab User 1 again. Complete MFA. Open the same account page. Sign out.
8. Disconnect the VPN now, before email, before the Azure portal, and before the next scenario.
9. Wait for the logs. Do not sign in a third time while you wait, or you will add noise.

If both sign-in rows show the same country, the geolocation of that VPN exit did not change country. You may try one different exit country, one time. Then stop, even if the countries still match. Record the VPN country you selected and the country the log displayed. A mismatch between the VPN label and Microsoft's geolocation is a useful note.

## What you should look for

`Entra ID` > `Monitoring & health` > `Sign-in logs` > user Lab User 1 > status success.

On the two successful rows:

* `Location` or the location detail shows city and country.
* The timestamps are inside the window you actually used.
* Both results are success (`0`) if MFA was completed.
* The IP addresses differ. You will redact the IPs in the screenshot. You still compare them privately.

Then run [detection.kql](detection.kql). It keeps a pair only when the user matches, the country differs, and the gap is at most 120 minutes.

## Identity Protection, only if you have P2

Open the sign-in and read the risk section, or open Identity Protection. Premium sign-in risk types that relate to this test are:

| `riskEventType` | What Microsoft calls it |
| --- | --- |
| `unlikelyTravel` | Atypical travel |
| `unfamiliarFeatures` | Unfamiliar sign-in properties |
| `newCountry` | New country |
| `anonymizedIPAddress` | Anonymous IP address |

Source: [What are risk detections?](https://learn.microsoft.com/entra/id-protection/concept-identity-protection-risks).

Atypical travel and unfamiliar sign-in properties need Entra ID P2. Anonymous IP is documented as a non premium detection, and it can still be missing. A new lab user is a poor history for "unfamiliar". Microsoft also describes a learning period for unfamiliar sign-in properties. Do not expect a risk hit. Screenshot the empty state when that is the truth.

`mcasImpossibleTravel` is a Defender for Cloud Apps detection. This lab does not require that product.

## After the test

VPN off. Then [response.md](response.md). Screenshots: checklist items 10, 11, and 12.
