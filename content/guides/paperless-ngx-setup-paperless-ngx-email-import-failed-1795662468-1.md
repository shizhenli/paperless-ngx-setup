---
title: "Paperless-ngx Email Import Fails: Diagnose the Mail Rule Before It Consumes"
description: "Troubleshoot Paperless-ngx email import failures in Docker: check mail rule filters, container logs, and attachment handling, then verify with a test message."
seo_title: "Paperless-ngx Email Import Failed: Fix Mail Rule Delivery"
seo_description: "Troubleshoot Paperless-ngx email import failures in Docker: check mail rule filters, container logs, and attachment handling, then verify with a test message."
keywords:
  - "paperless ngx email import failed"
date: 2026-11-28T22:16:04+08:00
publishDate: 2026-11-28T22:16:04+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You set up a mail rule in Paperless-ngx, the rule runs, but messages or attachments never reach the inbox. That is the **paperless ngx email import failed** state, and it usually comes down to one of three things: the rule filters, the container's mail connection, or attachment handling.

## Check the mail rule filters first

Open the mail rule in Paperless-ngx and confirm the account, folder, and filter settings match the messages you expect. A rule with a subject or sender filter that does not match will run and silently skip every message. Check that the rule is enabled and that the consumption folder is set to the watched import volume, not a path inside the container that does not exist.

## Read the container logs

Run docker logs on the Paperless-ngx container while the rule runs. Look for lines about connecting to the mail server, authentication, or fetching messages. If you see authentication errors, the account credentials in the rule are wrong. If you see no fetch lines at all, the rule is not being triggered or the scheduler is not running. If you see attachment errors, the message arrived but the attachment was rejected.

## Verify attachment handling

Paperless-ngx only imports supported file types. A message whose attachment is an unsupported format will be skipped. Check the rule's attachment settings and confirm the file type is one Paperless-ngx can consume. Also confirm the watched volume is writable by the container user.

## Verification test

Send a test email that matches the rule exactly, with a simple PDF attachment. Watch the logs and the inbox. If the document appears, the rule works. If not, note the last log line and adjust the matching setting.

## Rollback note

If a change makes things worse, disable the rule, restore the previous settings, and re-enable it. Keep a copy of the original rule configuration before editing.

If the logs stay silent after these checks,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult) with the log excerpt and rule settings.
