---
title: "Paperless-ngx Email Import Failed: Diagnose the Mail Rule and Restore Delivery"
description: "Troubleshoot Paperless-ngx email import when the mail rule runs but messages or attachments never reach the inbox. Check IMAP, filters, and container logs."
seo_title: "Paperless-ngx Email Import Failed: Fix Mail Rule Delivery"
seo_description: "Troubleshoot Paperless-ngx email import when the mail rule runs but messages or attachments never reach the inbox. Check IMAP, filters, and container logs."
keywords:
  - "paperless ngx email import failed"
date: 2026-11-14T19:32:27+08:00
publishDate: 2026-11-14T19:32:27+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You set up a mail rule in Paperless-ngx, the rule appears to run, but no messages or attachments ever arrive in the inbox. This is the classic **paperless ngx email import failed** symptom, and it usually comes down to a mismatch between what the rule expects and what the mail server actually delivers.

## Check the mail rule configuration first

Open the mail rule and confirm the IMAP server, port, username, and password. Then look at the folder and filter settings. A common trap is a filter that matches nothing, such as a subject keyword that never appears or a sender address with a typo. If the rule uses a specific folder, verify that folder exists on the server and that new mail is actually delivered there. Also confirm the rule is enabled and that its action is set to consume attachments or add to the inbox as intended.

## Inspect the container logs for the real error

Run `docker logs --tail 200 <container_name>` and look for lines mentioning mail, IMAP, or authentication. An authentication failure will show as a login error, while a filter mismatch often logs zero messages processed. If you see connection timeouts, check that the container can reach the mail server on the configured port. For TLS issues, confirm the correct encryption setting is selected.

## Verification test

Send a test email that exactly matches your rule's filter from an allowed sender. Watch the logs live with `docker logs -f <container_name>`. A successful import logs the message being processed and the document appearing in Paperless-ngx. If the logs show the message fetched but no document created, the attachment type or consumption action is the problem.

## Rollback and recovery

Before changing filters, note the original settings. If a change makes things worse, restore the previous rule and re-enable it. If messages were consumed but not stored, check the consumption folder for orphaned files before retrying.

If the logs remain silent,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult) with the exact log lines and rule settings.
