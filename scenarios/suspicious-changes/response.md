# Response: suspicious account changes

Remove the two lab changes after the screenshots exist. In a real incident you would remove an attacker's method and role first, and collect the audit row as you go. Do the same order here once the evidence files are saved.

## How to think about severity

* A new MFA method on an account that just had a password spray success or an unfamiliar sign-in: high. The attacker is trying to survive a password reset.
* A privileged role added by an unexpected admin: high. `Reports Reader` is deliberately low. Say that your lab role was low, and name which roles you would treat as critical (Global Administrator, Privileged Role Administrator, User Administrator, Security Administrator, Conditional Access Administrator, Application Administrator).
* A user registering their own first MFA method during onboarding: normal. Context is the difference. Your lab user adding a second method on a day you planned is expected. The same event with an actor you do not recognise is not.

## Triage

* Who is the actor on each audit row, and what is their IP?
* Is the actor the user, or an admin?
* Which method was added? Read the audit detail. Do not rely on the pop up you remember.
* Which role was added, was it active or eligible, and was it permanent?
* Were there other role events the same hour, especially removals of Conditional Access or additions of owners on applications? Those are outside this lab. If you see them and you did not cause them, stop the lab and investigate for real.

## Containment in this lab

1. Save screenshots 15, 16, and 17 first if you want them, because removal creates a new audit event and can clear the method from the user blade.
2. Sign in as Lab User 1. Open [https://mysignins.microsoft.com/security-info](https://mysignins.microsoft.com/security-info). Delete the extra method only. Keep the original Authenticator method until you disable the user in final cleanup.
3. As admin: `Entra ID` > `Roles & admins` > `Reports Reader` > assignments > remove Lab User 2.
4. Confirm Lab User 2 > `Assigned roles` no longer lists `Reports Reader`.
5. `Revoke sessions` on Lab User 1 and Lab User 2.
6. You do not need to reset passwords for this scenario unless triage made you unsure who added the method.

## What you would recommend outside a lab

* Alert on `User registered security info` and `Admin registered security info`, and on `Add member to role`, which is what `detection.kql` hunts.
* Privileged role assignment should be rare, ticketed, and eligible through Privileged Identity Management when you have Entra ID P2. P2 is not required to see the audit event.
* A Conditional Access user action for `urn:user:registersecurityinfo` can block security info registration outside a named location. That design is in `conditional-access/`. It needs P1. Test it in report only. If you turn it on while your own IP is outside the named location, you can block your own MFA registration.
* Remove unknown methods, revoke sessions, and reset the password. Removing the method without revoking sessions can leave an attacker signed in.
* Do not delete audit logs to tidy the tenant.

## Detection tuning

* The role filter is broad on purpose (`has "member to role"`). In a quiet lab that is fine. In a directory that uses Privileged Identity Management all day, you would add a filter on the role name and on the actor. Add that filter only after you have seen your real operation names, and paste the filter you used into the report.
* `User deleted security info` is in the query because attackers remove the real user's MFA method as well as adding their own. Your cleanup delete may appear. That is your activity. Label it as cleanup in the report so a reader does not think it was part of the attack.

## Analytics rule

Optional rule name: `LAB Account manipulation audit`. Severity High, because role changes deserve a look even when the lab role is weak. Disable it during cleanup. Map `Account` to `TargetUpn`. `ActorIp` can map to `IP` if you want the actor address.

## What I actually did

* MFA method type added, and the audit name: `[PLACEHOLDER]`
* Role and assignment type: `[PLACEHOLDER]`
* Time I removed each change: `[PLACEHOLDER]`
* Other audit events I did not expect: `[PLACEHOLDER: none, or describe them]`
