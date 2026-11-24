---
title: "Paperless-ngx Task Queue Stuck: Diagnose the Consume Queue Before It Clears"
description: "A file sits in the Paperless-ngx consume queue and never becomes searchable. Follow these Docker checks to diagnose a paperless ngx task queue stuck and restore normal processing."
seo_title: "Paperless-ngx Task Queue Stuck: Fix a File Stuck in Consume"
seo_description: "A file sits in the Paperless-ngx consume queue and never becomes searchable. Follow these Docker checks to diagnose a paperless ngx task queue stuck and restore normal processing."
keywords:
  - "paperless ngx task queue stuck"
date: 2026-11-24T08:04:17+08:00
publishDate: 2026-11-24T08:04:17+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

A file you dropped into the watched import volume is still sitting in the consume queue, and it never turns into a searchable document. Let's walk through this together, step by step, before you change anything permanent.

## Check the consumer container and its logs

First, confirm the consumer process is actually running. Run `docker compose ps` and look at the consumer service. If it shows `exited` or `restarting`, the queue has nothing to process it. Next, check the logs with `docker compose logs --tail=100 consumer`. Look for repeated errors such as a database connection failure, a missing OCR binary, or a permission error on the import volume. If the log ends with a clear exception, note the exact message; that is your real cause, not the queue itself.

## Confirm the file is visible inside the container

A common trap is a host path that is not mounted where Paperless-ngx expects it. Run `docker compose exec consumer ls -l /usr/src/paperless/consume` and compare the filename with what you placed on the host. If the file is absent, fix the volume mapping in your compose file. If it is present but owned by root while the container runs as a non-root user, correct the ownership. This is often why a **paperless ngx task queue stuck** condition appears permanent.

## Verification test

Place a small, known-good PDF into the watched volume and watch the consumer logs. A healthy run shows the file being picked up, OCR or parsing completing, and the document appearing in the web UI within a minute or two. If the test file also stalls, the problem is environmental, not file-specific.

## Rollback and recovery

Before restarting, copy the stuck file out of the consume directory to a safe location. Then restart only the consumer service. If processing still fails, restore your previous compose configuration and re-add the file. If you need a second pair of eyes,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult).
