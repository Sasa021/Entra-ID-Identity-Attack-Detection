# Response: impossible travel and unfamiliar location

Both sign-ins in this lab are yours. Practise the analyst steps on Lab User 1, then put the account back so parts 6 and 7 still work.

## How to think about severity

* Two countries, a short gap, and both sign-ins were your test: document it, revoke the lab session if you want the practice, and do not open a real incident against yourself.
* The same pattern when you did not travel and you did not use the VPN: treat the account as suspicious. Reset the password, revoke sessions, and check whether a new MFA method appeared (see the suspicious change scenario).
* Risk level `high` in Identity Protection, if you have P2 and it actually says high: raise the severity. If risk says `none` or the blade is missing, do not invent a high score. Your country pair is still the evidence.

## Triage

* What are the two cities and countries, and the minutes between them? Use the log timestamps.
* Did both succeed, and was MFA satisfied on each?
* Is the second IP a VPN you turned on? Check your notes before you call it hostile.
* Did the user register a new MFA method or get a new role on the same day? That combination is more serious than travel alone.
* Is this a new lab user with no normal country yet? Say that, so you do not over claim "unfamiliar" from a sample of two sign-ins.

## Containment in this lab

1. Confirm the VPN is disconnected.
2. `Entra ID` > `Users` > Lab User 1 > `Revoke sessions`.
3. Do not reset the password unless you no longer know who signed in. If you reset it, store the new password in your password manager. Later scenarios need this account.
4. Read the sign-in details for Conditional Access and for risk. Copy the words on the screen into the report.
5. If you have P2 and a risk detection is open, you may dismiss it as safe only because you caused it and you can say why. In a job, dismissing risk needs a reason. "Confirmed safe, lab VPN test on this date" is a reason. If there is no detection, there is nothing to dismiss.

## What you would recommend outside a lab

* Ask the user whether they travelled or used a VPN, using a known contact path. Do not use a phone number that arrived in the same suspicious session.
* If the user did not travel: reset the password, revoke sessions, and review MFA methods for a new registration.
* Named locations and "require MFA outside the home country" are the Conditional Access designs that match this scenario. They need Entra ID P1. They reduce success. They do not replace the hunt.
* Risk based MFA needs Entra ID P2. Without P2, keep the KQL hunt and the sign-in log review.
* Geolocation is often wrong for mobile networks and VPNs. A country change is a lead. It is not proof by itself.

## Detection tuning

* `MaxMinutes` is 120 so a slow portal login still pairs. A wider window creates more false pairs in a real directory. Record any change you made.
* Users who work across borders will trip this hunt. A named home country, or a maintained list of expected countries, is how you would reduce that. This lab does not add that list, because you have not measured a real travel pattern.
* The query only uses successful sign-ins. Failures from another country can still matter. If your second sign-in failed, the portal screenshot is the evidence, and you can say the KQL pair did not apply.

## Analytics rule

Optional scheduled rule name: `LAB Impossible travel country change`. Severity Medium. Disable it in cleanup. Map `Account` to `UserPrincipalName` and `IP` to `IPAddress`.

## What I actually did

* Countries and gap from the logs: `[PLACEHOLDER]`
* Sessions revoked: `[PLACEHOLDER]`
* Identity Protection action, or not licensed: `[PLACEHOLDER]`
* Query changes: `[PLACEHOLDER]`
