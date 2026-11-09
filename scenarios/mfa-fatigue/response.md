# Response: MFA fatigue

You denied your own prompts. Practise the user-facing response as if Lab User 1 had called the help desk, then put the account back into a known state.

## How to think about severity

* Several MFA denials and no success: the password may already be known to an attacker, even though MFA held. In this lab the "attacker" is you, so say that. The detection still matters.
* A success after a burst of denials: high priority, unless that success is the optional sixth attempt you approved on purpose.
* Prompts the user did not cause, continuing after the lab: treat it as a live incident. Reset the password and review every MFA method.

## Triage

* How many prompts, over how many minutes, and which error codes?
* Did any attempt succeed?
* Was number matching on, or was it a simple Approve and Deny? Write what the phone showed.
* Did the IP match your home IP from the earlier benign sign-in?
* Did a new MFA method get registered the same day?

## Containment in this lab

1. Stop at five prompts.
2. If you approved the optional sign-in, revoke sessions on Lab User 1.
3. If you did not approve any prompt, a session revoke is still reasonable practice. Do it.
4. Do not reset the password unless you want to. The password was typed by you and is still known to you. Record whether you reset it.
5. Open Lab User 1 > authentication methods. Confirm you recognise every method. You will add one extra method in the next scenario. If a method is already there that you did not add, remove it before you continue and describe it in the report.
6. If the account is locked or MFA is stuck, use the admin window to require a fresh registration only if you can still sign in as admin. Do not delete the only admin MFA method.

## What you would recommend outside a lab

* Turn on number matching for Microsoft Authenticator, so a tired user cannot tap Approve on a prompt they did not expect. This setting is in authentication methods, and it does not need a spray tool to test. Read the current blade before you change it: `Protection` > `Authentication methods` > `Policies` > `Microsoft Authenticator`. If you change it, change it back or leave it on and say which you did.
* Teach users to deny unexpected prompts and to report a burst, not to approve one to make it stop.
* Reset the password of a user who approves a prompt they did not start, and revoke sessions.
* MFA fatigue means the password is already in the attacker's hands. MFA held. The password is still burned.
* Conditional Access cannot by itself tell you that five prompts were denied. The hunt is the detection. MFA is the control that made the attacker need the prompt.

## Detection tuning

* Four correlation IDs in 30 minutes is a lab threshold. A real user who mistypes MFA twice should not page anyone. Write a threshold you would defend after you have seen how noisy your tenant is. You may not have that noise in a four user lab. Say so, and do not invent a false positive rate.
* Include the success-after-denial story in the report even though the main query counts failures. A failure only alert misses the moment someone gives in.

## Analytics rule

Optional rule name: `LAB MFA fatigue prompts`. Severity Medium. Disable it during cleanup.

## What I actually did

* Prompts denied: `[PLACEHOLDER]`
* Optional approval: `[PLACEHOLDER]`
* Error codes: `[PLACEHOLDER]`
* Sessions revoked: `[PLACEHOLDER]`
* Number matching left as: `[PLACEHOLDER: unchanged, on, or off]`
