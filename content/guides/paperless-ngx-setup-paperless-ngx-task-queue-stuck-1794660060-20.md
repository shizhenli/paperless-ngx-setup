---
title: "Paperless-ngx Task Queue Stuck: Diagnose a File That Never Leaves the Consume Queue"
description: "A file remains in the Paperless-ngx consume queue and never becomes searchable. Follow these checks to diagnose the stuck task and recover safely."
seo_title: "Paperless-ngx Task Queue Stuck: Fix a Stalled Consume Queue"
seo_description: "A file remains in the Paperless-ngx consume queue and never becomes searchable. Follow these checks to diagnose the stuck task and recover safely."
keywords:
  - "paperless ngx task queue stuck"
date: 2026-11-21T15:17:41+08:00
publishDate: 2026-11-21T15:17:41+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

A file sits in your Docker watched import volume, but the consume queue never moves it to a searchable document. This is a classic **paperless ngx task queue stuck** situation, and it usually means the consumer is waiting on a resource or a failed task rather than processing normally.

## Check the queue and worker state

First, open the Paperless-ngx admin or dashboard and look at the task queue. A task that stays in the pending or started state for more than a few minutes is the signal. Next, run `docker compose ps` and confirm the `webserver` and `consumer` containers are both up. Then check the consumer logs with `docker compose logs --tail=100 consumer`. Look for repeated retries, lock errors, or a message that the file is unreadable. If the log shows the same file being retried, note its name and path.

## Verify the import volume and file

Confirm the file actually exists inside the container: `docker compose exec webserver ls -l /usr/src/paperless/consume`. If the file is missing, your volume mount is wrong. If it is present, check its permissions and that it is a supported format. A zero-byte or partially copied file will stall the queue. Also confirm the consume directory is not read-only.

## Verification test

Drop a small, known-good PDF or TXT file into the watched volume. Watch the queue for two minutes. If the test file becomes a searchable document, the original file was the problem. If the test file also stalls, the consumer itself needs attention.

## Recovery note

Before changing anything, copy the stuck file out of the consume volume. If you restart the consumer and the task still does not clear, move the file to a temporary folder, restart the stack, and re-import it once. This avoids a loop without deleting your source. If the queue remains stuck after these checks,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult) with the consumer log excerpt and the file details.
