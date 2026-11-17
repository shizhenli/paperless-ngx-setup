---
title: "Paperless-ngx Email Import Failed: When the Mail Rule Runs but Messages Never Arrive"
description: "Troubleshoot Paperless-ngx email import when the mail rule runs but messages or attachments never reach the inbox. Step-by-step checks, verification test, and recovery note."
seo_title: "Paperless-ngx Email Import Failed: Fix Mail Rule Delivery"
seo_description: "Troubleshoot Paperless-ngx email import when the mail rule runs but messages or attachments never reach the inbox. Step-by-step checks, verification test, and recovery note."
keywords:
  - "paperless ngx email import failed"
date: 2026-11-17T09:12:48+08:00
publishDate: 2026-11-17T09:12:48+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You set up a mail rule in Paperless-ngx, the rule appears to run, yet messages and attachments never reach the inbox, leaving you with a **paperless ngx email import failed** condition.

## Confirm the rule is actually matching messages
Open the mail rule and check the filter fields: folder, subject, from, and attachment type. A rule that runs but matches nothing fails silently. Set the folder to INBOX and temporarily clear subject and sender filters, then save. If the rule still does not fire, the problem is matching, not delivery.

## Check the mail account connection
In the same rule, verify the host, port, username, and password. For IMAP use port 993 with SSL; for STARTTLS use 143. A wrong port or an expired app password causes a connection failure that the UI may not surface. Save the rule and watch the container log for authentication or timeout errors.

## Inspect the consume path inside Docker
Because you use a watched import volume, confirm the mail rule's consumption folder maps to a path the container can write. Run `docker exec -it <container> ls -la /usr/src/paperless/consume` and check ownership. If the folder is missing or not writable by the paperless user, attachments are fetched but discarded. Fix permissions with `chown -R 1000:1000` on the host volume, then restart the container.

## Verification test
Send a test email with a single PDF attachment to the monitored address. Within one polling cycle, run `docker logs --tail 50 <container>` and look for a consume task referencing the attachment filename. Then check Documents for the new file. Success means the log shows the task and the document appears; failure means the log shows a connection or permission error you can now target.

## Rollback note
If clearing filters or changing ports breaks other rules, restore the previous rule settings from your configuration backup before continuing. If the cause remains unclear,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult) with the relevant log lines.
