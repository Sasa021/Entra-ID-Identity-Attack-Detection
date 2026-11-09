# MFA fatigue simulation (T1621)

You sign in as Lab User 1 with the correct password and you deny the Authenticator prompt five times. That is the whole attack. [T1621](https://attack.mitre.org/techniques/T1621/) is Multi-Factor Authentication Request Generation: the attacker already has the password and spams MFA until the person approves one.

You are the person and the attacker. You stop at five.

## Stay inside this tenant

* Only Lab User 1.
* Only prompts on your own Authenticator registration.
* No MFA bombing tool, no team, no script that retries `login.microsoftonline.com`.
* VPN off, so this test is not mixed with the travel test.
* If the prompts stop appearing, Microsoft is throttling the account. Stop. Write "throttled" in the report. Do not look for a bypass.

## Before you start

* Lab User 1 has Microsoft Authenticator registered, and a push or number match prompt appears when you sign in. Part 1 of the lab guide is where you register it.
* If no prompt appears, security defaults are probably off and no Conditional Access policy requires MFA. Turn security defaults on, register Lab User 1, and come back. Do not build a tool to force the prompt.
* Your admin session is open in another window.
* The phone is next to you, unlocked, on the Authenticator account for Lab User 1.

## Steps

1. Private window, VPN off.
2. Go to [https://login.microsoftonline.com](https://login.microsoftonline.com).
3. Sign in as Lab User 1 with the correct password.
4. When the phone prompts, choose Deny. If the phone only lets the prompt expire, let it expire. Do not approve.
5. Repeat from step 3 until you have done this 5 times. Count the prompts, not the page refreshes.
6. Stop at 5. Do not continue to 10.
7. Close the private window.

Optional sixth sign-in: approve one prompt, then sign out. This shows a success after repeated denials. Skip it if you are tired of prompts or the tenant has started throttling. Write `done` or `not done` in the report. If you approve, revoke that session in the response steps so the token does not linger.

## What you should look for

Sign-in logs for Lab User 1 during the window:

* Error `500121`: the user did not complete the MFA prompt. Microsoft documents this code for a prompt that was cancelled or not finished. Source: [sign-in error codes](https://learn.microsoft.com/entra/identity/monitoring-health/howto-troubleshoot-sign-in-errors).
* You may also see `50074` (strong authentication did not complete) or a row that only says MFA is required. Record the codes on your rows.
* Five failures close together, same user.
* If you approved the optional attempt: one later success with MFA.

The hunt in [detection.kql](detection.kql) counts distinct `CorrelationId` values for `500121` and `50074`. If your tenant used a different code, add that code and note it.

Entra ID P2 has a premium detection called suspicious MFA authentication approval (`authenticatorPhishing` in the risk documentation). It is aimed at an approval that looks like a phishing prompt. Five denials may not create it. Absence is a valid result.

## After the test

Run the hunt. Follow [response.md](response.md). Screenshots: checklist items 13 and 14.
