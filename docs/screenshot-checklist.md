# Screenshot checklist

Take these after the matching step in [lab-guide.md](lab-guide.md). Save the redacted file under `evidence/` with the name below. Rules for what to cover are in [evidence/README.md](../evidence/README.md).

If a step does not apply, do not invent the image. Add a line in the report that says `Not collected`, and the reason (licence, trial declined, or a control you left as a design).

A real empty table is worth saving when the lab guide told you to run that query.

1. **File:** `evidence/01-licence-and-security-defaults.png`
   **Show:** The Entra or Microsoft 365 licence blade with the licence you actually have (Free, P1, P2, or the trial name), and the security defaults blade showing Enabled or Disabled.
   **Redact:** Tenant ID if it is on the same screen. Your admin user principal name.
   **When:** Lab guide part 1.1.

2. **File:** `evidence/02-test-users-and-emergency-accounts.png`
   **Show:** The users list with Lab User 1 to Lab User 4 and Emergency Access 1 and Emergency Access 2. The `Lab Identity Users` group membership is useful in this shot or in a second crop of the same checkpoint.
   **Redact:** The tenant domain on each user principal name. Object IDs.
   **When:** Lab guide part 1.3 and 1.4.

3. **File:** `evidence/03-emergency-account-signin.png`
   **Show:** A successful sign-in log row for one emergency access account, taken before any lab Conditional Access policy is set to `On`. The result should be success, and the user column should be that emergency account.
   **Redact:** IP address, tenant domain, and the full user principal name if you do not want the `onmicrosoft.com` name public. Leave the success result and the time visible.
   **When:** Lab guide part 1.5.

4. **File:** `evidence/04-daily-cap-or-budget.png`
   **Show:** The Log Analytics daily cap page with the cap value visible, or the Cost Management budget that covers the Sentinel resource group if you deliberately did not change the cap. The resource name can stay if it is not sensitive. Cover subscription IDs.
   **Redact:** Subscription ID, and any email address in the alert recipient list.
   **When:** Lab guide part 2.1 and 2.2, before you rely on the connector.

5. **File:** `evidence/05-entra-connector-signin-and-audit.png`
   **Show:** The Microsoft Entra ID data connector (or the diagnostic setting `entra-identity-lab`) with sign-in logs and audit logs selected or connected. Noisy log types should be visible as not selected, if the page shows them.
   **Redact:** Workspace resource IDs if the page shows a full Azure resource ID and you do not want the subscription GUID public. The workspace name can stay.
   **When:** Lab guide part 2.4. If the portal blocks sign-in logs because of licensing, capture that message instead and say so in the report.

6. **File:** `evidence/06-first-signinlogs-row.png`
   **Show:** The Sentinel `Logs` blade with the part 3 `SigninLogs` query and at least one real row for Lab User 1. If the table does not exist, capture the error text from the query window.
   **Redact:** IP address and user principal name domain.
   **When:** Lab guide part 3.

7. **File:** `evidence/07-first-auditlogs-row.png`
   **Show:** The part 3 `AuditLogs` query and at least one real audit row from creating the lab, or the error if the table is missing.
   **Redact:** User principal names and IP addresses inside the result.
   **When:** Lab guide part 3.

8. **File:** `evidence/08-password-spray-failures.png`
   **Show:** Sign-in logs filtered so the failures from the spray are visible: more than one lab user, error code `50126` or the code you actually received, one source IP, and timestamps inside one short window. The Entra sign-in log blade is the right place if Sentinel has no `SigninLogs`.
   **Redact:** The IP address and user principal name domains. Leave the error code, the result, and the time visible.
   **When:** Lab guide part 4.

9. **File:** `evidence/09-password-spray-kql.png`
   **Show:** `scenarios/password-spray/detection.kql` in the Sentinel query editor, including the result grid. If you changed `MinUsers` or `MinFailures`, the new numbers must be visible in the query text.
   **Redact:** IP addresses and user principal names in the grid.
   **When:** Lab guide part 4. If you had no `SigninLogs` table, skip this file and write `Not collected: sign-in logs were not streamed`.

10. **File:** `evidence/10-impossible-travel-two-locations.png`
    **Show:** Two successful sign-in rows for Lab User 1, with timestamps close enough to match the test, and two different countries or cities in the location column.
    **Redact:** IP addresses. City and country can stay, because the location is the point of the evidence. Redact the user principal name domain.
    **When:** Lab guide part 5.

11. **File:** `evidence/11-impossible-travel-kql.png`
    **Show:** `scenarios/impossible-travel/detection.kql` and the result grid. A zero row result is acceptable if screenshot 10 shows the portal rows and the report explains the gap (blank country, same country, or time window).
    **Redact:** IP addresses and user principal names.
    **When:** Lab guide part 5. Skip with a written reason if `SigninLogs` was not available.

12. **File:** `evidence/12-identity-protection-or-empty.png`
    **Show:** One true screen. Either a risk detection on Lab User 1 (`unlikelyTravel`, `unfamiliarFeatures`, `newCountry`, `anonymizedIPAddress`, or another type the portal shows), or the Identity Protection blade showing that risk detection is not in your licence, or a sign-in detail where risk is `none`.
    **Redact:** IP addresses and user principal names.
    **When:** Lab guide part 5. Do not add a risk event the portal did not return.

13. **File:** `evidence/13-mfa-fatigue-failures.png`
    **Show:** Several sign-in rows for Lab User 1 from the fatigue test, with error `500121` or the MFA failure code shown on your tenant, across the five attempts.
    **Redact:** IP address and user principal name domain.
    **When:** Lab guide part 6.

14. **File:** `evidence/14-mfa-fatigue-kql.png`
    **Show:** `scenarios/mfa-fatigue/detection.kql` and the result grid, including the attempt count the query calculated.
    **Redact:** User principal name and IP addresses.
    **When:** Lab guide part 6. Skip with a written reason if `SigninLogs` was not available.

15. **File:** `evidence/15-mfa-method-registered-audit.png`
    **Show:** The audit log detail for `User registered security info` (or `Admin registered security info` if an admin did it) for Lab User 1. The activity name, result, actor, and target should be readable.
    **Redact:** Phone numbers, device names you do not want public, IP address, and user principal name domains.
    **When:** Lab guide part 7.

16. **File:** `evidence/16-role-assignment-audit.png`
    **Show:** The audit log detail for `Add member to role` where the role is `Reports Reader`, or the low privilege role you actually assigned. Actor, target, and result should be readable.
    **Redact:** User principal name domains and IP address. Leave the role name visible.
    **When:** Lab guide part 7.

17. **File:** `evidence/17-suspicious-change-kql.png`
    **Show:** `scenarios/suspicious-changes/detection.kql` and a result grid that includes the MFA registration event and the role assignment, if both were ingested.
    **Redact:** User principal names, IP addresses, and the modified properties field if it contains a phone number.
    **When:** Lab guide part 7. This one can exist even when sign-in logs were not streamed, because it uses `AuditLogs`.

18. **File:** `evidence/18-conditional-access-or-security-defaults.png`
    **Show:** Either the Conditional Access policy list with each `LAB DESIGN` policy and its state (`Report-only` or `Off`), or the security defaults page if you did not deploy Conditional Access.
    **Redact:** Account names in the exclude list if they expose the emergency account user principal name. You can leave the policy names visible.
    **When:** Lab guide part 8.

19. **File:** `evidence/19-conditional-access-report-only.png`
    **Show:** One sign-in log open on the Conditional Access tab, with at least one `LAB DESIGN` policy and a report only result.
    **Redact:** IP address and user principal name.
    **When:** Lab guide part 8. If you did not deploy a policy, do not create this file. Write `Not collected`.

20. **File:** `evidence/20-cleanup.png`
    **Show:** Evidence that the lab access was removed. One image can be a single blade. Two files are allowed when one screen cannot show it all: `evidence/20a-role-removed.png` and `evidence/20b-policies-off.png`. Together they should show the role removed from Lab User 2, or the user deleted, and the lab policies set to `Off` or absent. If you turned security defaults back on, that toggle is a good final frame.
    **Redact:** The same identity fields as the earlier shots.
    **When:** Lab guide part 10.

Optional, only if you created one:

* **File:** `evidence/21-analytics-rule-disabled.png`
* **Show:** The scheduled analytics rule you created, including the severity and the query, and the rule set to `Disabled` after the test.
* **Redact:** Any entity values in a sample alert. If no alert fired, do not fake an incident blade.
