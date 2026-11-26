---
title: "Paperless-ngx Task Queue Stuck: When a File Never Leaves the Consume Queue"
description: "Troubleshoot a Paperless-ngx task queue stuck on a file in the consume queue. Check worker status, volume permissions, and logs, then verify with a test file."
seo_title: "Paperless-ngx Task Queue Stuck: Fix a File Stuck in Consume Queue"
seo_description: "Troubleshoot a Paperless-ngx task queue stuck on a file in the consume queue. Check worker status, volume permissions, and logs, then verify with a test file."
keywords:
  - "paperless ngx task queue stuck"
date: 2026-11-26T09:03:22+08:00
publishDate: 2026-11-26T09:03:22+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

A file sits in your watched import volume, but Paperless-ngx never moves it past the consume queue into a searchable document.

## Confirm the queue state

Open the admin or API task list and note the task ID for the stuck file. Check that the task status is pending or started, not failed. A started task that never advances usually means the consumer worker is blocked. Run `docker compose ps` and confirm the `webserver` and `consumer` containers are up. If the consumer is restarting, the queue will not drain.

## Inspect worker logs and volume access

Run `docker compose logs --tail=100 consumer`. Look for permission errors, missing files, or repeated retries. Then verify the watched volume is mounted read-write and that the container user can read the file. A file owned by root with restrictive permissions can leave the task waiting. Also check that the file is fully copied; a partial upload can stall consumption.

## Clear the stuck task safely

If the task remains pending after the worker is healthy, cancel it from the task list, move the file out of the consume directory, and re-add it. This is your rollback: keep the original file until the new task completes, so you can restore it if needed.

## Verification test

Create a small text file in the watched volume. Watch the task list until it reaches success, then search for the document title. If that test file becomes searchable, the queue is working. If it also stalls, the problem is environmental, not file-specific.

If the queue still does not drain, treat it as a **paperless ngx task queue stuck** condition and gather the task ID, consumer logs, and volume permissions before changing configuration. For guided diagnosis,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult).
