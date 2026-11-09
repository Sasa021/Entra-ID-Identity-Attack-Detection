# Password spray simulation (T1110.003)

This is a manual test against four lab users in your own Entra tenant. You type three wrong passwords once each. You do not run a tool, and you do not use a list taken from a breach.

Parent technique: [T1110 Brute Force](https://attack.mitre.org/techniques/T1110/). This pattern is [T1110.003 Password Spraying](https://attack.mitre.org/techniques/T1110/003/): one small set of passwords, many accounts, so each account sees only a few failures.

## Stay inside this tenant

* Targets are Lab User 1, Lab User 2, Lab User 3, and Lab User 4 from [docs/lab-guide.md](../../docs/lab-guide.md).
* The sign-in page is Microsoft's, and the username is one of those four lab users.
* Do not type a username from any other domain.
* Do not use Spray, MSOLSpray, FireProx, a VPS, or a script.
* Do not download a password list.
* Stop after one pass, or sooner if the site locks an account or stops responding.

## Before you start

* Part 1 to part 3 of the lab guide are done.
* VPN is off.
* Your working admin is signed in on a separate browser window, in case you need to reset a lab user.
* You are not using the emergency access accounts for this test.

## Steps

1. Open a private window.
2. Go to [https://login.microsoftonline.com](https://login.microsoftonline.com).
3. For each lab user, in this order, try these three passwords and no others. Type them. Do not paste from a wordlist file.

| Order | User | Passwords, in this order |
| --- | --- | --- |
| 1 | Lab User 1 | `LabWrong!A`, then `LabWrong!B`, then `LabWrong!C` |
| 2 | Lab User 2 | `LabWrong!A`, then `LabWrong!B`, then `LabWrong!C` |
| 3 | Lab User 3 | `LabWrong!A`, then `LabWrong!B`, then `LabWrong!C` |
| 4 | Lab User 4 | `LabWrong!A`, then `LabWrong!B`, then `LabWrong!C` |

4. After each attempt, the page should reject the password. That is the point. Do not keep trying a fourth password on the same user.
5. One pass is 12 attempts. Do not start a second pass.
6. Close the private window.
7. Write down the clock time you started and finished. You will compare that to the log later. The log time is the one that goes in the report if they differ.

## Optional success after the failures

This step is optional. It shows what a spray looks like when one password was right. Skip it if any account locked.

1. New private window, same home IP, VPN still off.
2. Sign in as Lab User 4 with the real lab password.
3. If MFA prompts, you may complete it or deny it. Record which you did.
4. Sign out.

Lab User 4 is the optional success so it stays separate from Lab User 1, who is used for travel and MFA fatigue.

## What you should look for

In `Entra ID` > `Monitoring & health` > `Sign-in logs`, filter to the last 24 hours and look for the four lab users.

You are hoping to see, because this is what the test is built to produce. If you see something else, record what you see.

* Result: failure.
* Error code `50126` (invalid username or password) on the wrong password attempts.
* The same IP address on those rows.
* Timestamps inside the few minutes you were typing.
* Client app: browser.
* If you did the optional success: one success (`0`) for Lab User 4 from that same IP afterwards.

Other codes you might hit if you went too far, or if the account state got in the way:

| Code | Meaning in Microsoft's sign-in troubleshooting |
| --- | --- |
| `50126` | Invalid username or password |
| `50053` | Account locked after too many wrong passwords |
| `50055` | Password expired |
| `50056` | Invalid or null password |
| `50057` | Account disabled |

Source: [How to troubleshoot sign-in errors](https://learn.microsoft.com/entra/identity/monitoring-health/howto-troubleshoot-sign-in-errors). If your row uses a code that is not in this table, copy that code into the report and into the KQL list.

Smart lockout may lock a user if failures pile up. Microsoft has documented a default near 10 failures, with a short and then growing lockout. Three failures per user is the plan so you stay under that. If you still see `50053`, stop. From the admin window, reset that lab user's password or wait for the lockout to clear. Do not keep spraying a locked account.

## Entra ID P2 note

Identity Protection has a premium `passwordSpray` detection. Microsoft says it fires when a spray **successfully** validates a password, not merely when passwords fail, and it needs Entra ID P2. Your KQL hunt looks at the failures, which is what you can see without P2. If the premium detection is absent, that matches either the licence or the fact you only sent wrong passwords.

## After the test

Run [detection.kql](detection.kql). Then follow [response.md](response.md). Screenshots: checklist items 8 and 9.
