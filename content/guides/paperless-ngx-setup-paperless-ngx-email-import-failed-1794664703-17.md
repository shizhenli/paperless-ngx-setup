---
title: "Paperless-ngx Email Import Failed: Check the Consume Directory and Mail Rule"
description: "Troubleshoot Paperless-ngx email import when the mail rule runs but messages or attachments never reach the inbox. Check the consume directory, permissions, and rule filters."
seo_title: "Paperless-ngx Email Import Failed: Fix the Consume Directory"
seo_description: "Troubleshoot Paperless-ngx email import when the mail rule runs but messages or attachments never reach the inbox. Check the consume directory, permissions, and rule filters."
keywords:
  - "paperless ngx email import failed"
date: 2026-11-22T10:04:21+08:00
publishDate: 2026-11-22T10:04:21+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You set up a mail rule in Paperless-ngx, but messages or attachments never appear in the inbox even though the rule runs.

## Confirm the rule is actually processing mail

Open the Paperless-ngx admin, go to Mail rules, and check that the rule is enabled and its filter matches the test message. Then look at the task queue under Tasks. A successful fetch shows a completed task with a message count; a failed fetch shows an error. If the task completes but the inbox stays empty, the problem is usually the consume directory, not the mail server.

## Check the consume directory inside the container

Paperless-ngx writes imported mail attachments to the consume directory before ingestion. Run `docker exec -it <container> ls -la /usr/src/paperless/consume` and confirm the directory exists and is writable by the paperless user. A missing directory or permission denied here is the most common cause of **paperless ngx email import failed** symptoms. If the directory is empty after a fetch, verify the mail rule's attachment setting and that the message actually contains an attachment.

## Verify with a controlled test

Send a plain email with one small PDF attachment to the watched mailbox. Watch the Tasks page for a new fetch task, then check the consume directory again. If the file appears there but not in Documents, the consumer is the issue; if it never appears, revisit the mail rule filter and credentials.

## Rollback and recovery

If a change breaks imports, disable the mail rule, restore the previous consume path in your Docker volume mapping, and restart the container. Keep a copy of any files already in consume before adjusting permissions.

For hands-on help with your specific Docker layout,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult).
