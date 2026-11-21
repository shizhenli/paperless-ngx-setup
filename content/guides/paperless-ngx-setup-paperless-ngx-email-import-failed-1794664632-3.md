---
title: "Paperless-ngx Email Import Fails: Check the Mail Rule and Attachment Handling"
description: "Mail rule runs but messages or attachments never reach Paperless-ngx? Diagnose the mail rule, attachment handling, and Docker volume with this practical troubleshooting guide."
seo_title: "Paperless-ngx Email Import Failed: Troubleshoot Mail Rule and Attachments"
seo_description: "Mail rule runs but messages or attachments never reach Paperless-ngx? Diagnose the mail rule, attachment handling, and Docker volume with this practical troubleshooting guide."
keywords:
  - "paperless ngx email import failed"
date: 2026-11-21T15:17:41+08:00
publishDate: 2026-11-21T15:17:41+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You set up a mail rule in Paperless-ngx, the rule runs, but messages or attachments never appear in your inbox—so you're stuck at the email import fails step without a controlled diagnosis.

## Check the mail rule configuration

Open Settings > Mail and select your rule. Confirm the rule is enabled and the account credentials are correct. A common cause is a mismatch between the rule's filter (subject, sender, or body) and the actual incoming mail. Temporarily broaden the filter to match any message, then send a test email. If the rule still does not trigger, the problem is upstream of Paperless-ngx—verify the mailbox is reachable and the IMAP folder is correct.

## Inspect attachment handling

If the rule triggers but nothing is imported, examine the attachment settings. Paperless-ngx only imports attachments it recognizes as documents. Check that the rule's attachment type is set to process attachments, and that the file extension is not being filtered out. For PDFs, confirm the file is not password-protected. If your mail rule is set to consume the email body only, attachments will be ignored—this is a frequent cause of **paperless ngx email import failed** symptoms.

## Verify the Docker volume and permissions

The consumed files must land in the watched import volume. Run `docker exec -it paperless-ngx ls -la /usr/src/paperless/consume` and confirm the directory exists and is writable by the container user. If permissions are wrong, the import silently fails. Fix ownership with `chown -R 1000:1000` on the host volume, then restart the container.

## Verification test

Send a test email with a small PDF attachment. Watch the container logs with `docker logs -f paperless-ngx`. You should see the mail rule trigger and the document enter the consume queue. If the log shows the rule firing but no document appears, recheck attachment settings.

## Rollback note

If changes make things worse, revert the mail rule to its previous filter and attachment settings, then restart the container. For deeper diagnosis,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult).
