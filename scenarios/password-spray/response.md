# Response: password spray

Use this after you have the sign-in rows from your own test. The containment steps below are for lab users you created. In a real shift you would confirm the user with the business before you reset a password. Here, you are the business.

## How to think about severity

Decide after you read the logs. A useful starting point for this pattern:

* Many accounts, all failures, no success: the passwords were not accepted. In a real tenant this is still urgent, because the attacker often comes back, and legacy authentication may be a second door.
* A success from the same IP during or just after the failures: treat that account as compromised until you prove the success was your optional lab step.
* Error `50053` on many users: the spray is large enough to lock people out. That is an availability problem as well as a credential problem.

Write the severity you chose in the report, and why.

## Triage

Answer these from the logs, not from memory.

* Which IP addresses failed, and how many users did each IP touch?
* Which error codes appeared?
* Did any user succeed from that IP afterwards? Was that your optional Lab User 4 step?
* What client app was used? Browser is what this lab produces. `IMAP`, `POP`, `SMTP`, or other legacy clients would matter more in a real attack because they bypass MFA.
* Did Conditional Access or security defaults already block anything?
* Is the IP a network you recognise as home?

## Containment in this lab

1. Stop the test. Do not run a second pass.
2. From the working admin window, open each lab user that shows `50053` or that you cannot explain.
3. `Entra ID` > `Users` > select the user > `Reset password`. Set a new lab password in your password manager.
4. On the same user, select `Revoke sessions` so any token from the optional success is dropped.
5. If you want to practise a disable: `Properties` > account enabled, or the block sign-in control, set so the user cannot sign in. Re-enable the user before a later scenario that needs them. Do not disable your admin or the emergency accounts.
6. Leave smart lockout settings at the default. The blade is `Protection` > `Authentication methods` > `Password protection`. Custom values need Entra ID P1.

## What you would recommend outside a lab

These are recommendations to write in the report. You do not need a second tenant to make them.

* Keep legacy authentication blocked, with security defaults or with the block legacy Conditional Access design.
* Require MFA for users, so a correct password is not a finished intrusion.
* Alert on one IP failing across many users, which is the hunt in `detection.kql`.
* Do not publish the source IP as a "bad IP" for the whole internet based on one lab. In this test the source IP is your home address.
* Reset the password and revoke sessions for any account that succeeded and was not an approved test.
* If the same pattern continues after you stop, it is no longer your test. Contain the accounts and check that nobody else has the passwords.

## Detection tuning

* A 15 minute bin can split a slow pass into two bins. If your lab rows split, say so, and decide whether a 30 minute bin is honest for the way you typed.
* The default thresholds are for this four user lab. In a larger directory, the same numbers would be noisy. Write what you would raise them to, and why, once you have seen your own row counts.
* Failures of one user many times are password guessing ([T1110.001](https://attack.mitre.org/techniques/T1110/001/)), not spraying. This query asks for several users on purpose.

## Analytics rule

If you create the optional scheduled rule from the lab guide, name it `LAB Password spray failures`, severity Medium, threshold greater than 0, and disable it during cleanup.

## What I actually did

Complete this after the lab.

* Severity I assigned: `[PLACEHOLDER]`
* Accounts I reset or left as they were: `[PLACEHOLDER]`
* Sessions I revoked: `[PLACEHOLDER]`
* Query changes: `[PLACEHOLDER]`
* Follow up I still owe: `[PLACEHOLDER]`
