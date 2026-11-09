# Evidence

This folder is empty on purpose. Add screenshots only after you have done the lab yourself. Do not draw, edit, or generate log rows, alerts, counts, or findings.

The list of files to add is in [docs/screenshot-checklist.md](../docs/screenshot-checklist.md). Use those file names.

## Redact before you commit

Screenshots from Entra ID and Sentinel often contain private values. Check every image before you commit it, including the address bar and the account chip in the corner of the browser.

Cover these with a solid box in an image editor:

* Public IP addresses
* Tenant ID, also called the directory ID
* Object IDs, when they are not needed to understand the screenshot
* User principal names and email addresses. Cover the tenant domain if you do not want that domain to be public. A label such as `labuser1` is enough.
* Phone numbers, and any MFA registration detail that shows a number or device name you do not want to publish
* Passwords, temporary passwords, app passwords, and recovery codes
* Tokens, cookies, client secrets, and certificate data
* A home street address, if a location blade shows one

Keep the original unredacted images outside Git, in `evidence/unredacted/`. That path is listed in `.gitignore`. Commit only the redacted copy, under the file name in the checklist.

Do not commit CSV exports, HAR files, or diagnostic dumps from the portal. Those files repeat the same secrets and are easy to miss. The `.gitignore` file ignores `*.csv` and `*.har` for this reason.

A screenshot that shows no rows is valid evidence. Keep it, and write in the report what you expected to see and what you checked next. Do not replace an empty result with a made up one.
