---
title: "Paperless-ngx Document Stuck in Consume: Diagnose the Queue Before It Clears"
description: "A file remains in the consume queue and never becomes searchable. Learn how to diagnose a paperless ngx document stuck in consume with Docker volume checks and verification."
seo_title: "Paperless-ngx Document Stuck in Consume: Fix the Queue"
seo_description: "A file remains in the consume queue and never becomes searchable. Learn how to diagnose a paperless ngx document stuck in consume with Docker volume checks and verification."
keywords:
  - "paperless ngx document stuck in consume"
date: 2026-11-15T18:51:33+08:00
publishDate: 2026-11-15T18:51:33+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You've dropped a file into the watched import volume, but it stays in the consume queue and never appears as a searchable document.

## Check the Consumer Logs First

Run `docker compose logs -f webserver` (or your consumer service name) and look for lines mentioning the file. If you see repeated "Consuming" messages without a completion, the consumer is retrying. If you see a permission error, the container user cannot read the file. A silent log with no mention of the file means the volume mount is wrong.

## Verify the Watched Volume Mount

Run `docker compose exec webserver ls -la /usr/src/paperless/consume` and compare the output to the host directory. If the file is missing inside the container, your compose volume path is incorrect. If it appears with owner `root` and your container runs as a non-root user, fix ownership with `chown -R 1000:1000` on the host.

## Confirm the Consume Directory Setting

Check `PAPERLESS_CONSUME_DIR` in your environment file. It must match the container path, not the host path. A mismatch leaves files in a directory the consumer never scans, which is a common cause of a **paperless ngx document stuck in consume**.

## Verification Test

Create a small text file in the watched volume and watch the logs. A healthy consumer logs the file, processes it, and the document appears in the UI within a minute. If it does not, the queue is still blocked.

## Rollback and Recovery

If you changed ownership or paths, revert to the previous compose configuration and restart the stack. Remove any partially consumed file from the queue before retrying to avoid duplicate entries.

If the queue still will not clear,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult) with your logs and compose file.
