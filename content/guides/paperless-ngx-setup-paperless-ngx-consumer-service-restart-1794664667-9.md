---
title: "Paperless-ngx Consumer Service Restart: Clear a Stuck Consume Queue"
description: "A file sits in the consume queue and never becomes a searchable document. Learn how to diagnose the consumer service and perform a safe paperless ngx consumer service restart."
seo_title: "Paperless-ngx Consumer Service Restart: Clear a Stuck Consume Queue"
seo_description: "A file sits in the consume queue and never becomes a searchable document. Learn how to diagnose the consumer service and perform a safe paperless ngx consumer service restart."
keywords:
  - "paperless ngx consumer service restart"
date: 2026-11-21T15:23:52+08:00
publishDate: 2026-11-21T15:23:52+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You uploaded a file to the watched import volume, but it remains in the consume queue and never becomes a searchable document. Here is a calm, step-by-step diagnosis before you restart anything.

## Check the consumer service status

First, confirm the consumer container is running. Run `docker ps` and look for the paperless consumer service. If it is missing or restarting, note the container name. Then check its logs with `docker logs --tail 50 <consumer-container>`. Look for a repeated error, a permission denial, or a message that the task is waiting on a lock. If the log shows a clear failure, fix that cause first; a restart alone may not help.

## Interpret the queue state

Open the Paperless-ngx web interface and inspect the task queue. If the file shows as pending with no progress, the consumer may be idle or blocked. If it shows as started but never finishes, a worker may be hung. In both cases, a controlled **paperless ngx consumer service restart** is reasonable after you have captured the logs. Use `docker restart <consumer-container>` and watch the logs again for a few minutes.

## Verification test

Drop a small, known-good PDF into the watched volume. Wait for the consumer to pick it up. Then search for its title in the web interface. Success means the document appears and opens with text. If the original file is still stuck, remove it from the queue and re-add it after the restart.

## Rollback and recovery

If the restart does not clear the queue, stop the consumer, move the stuck file out of the watched volume, and restart the stack. Keep the original file safe so you can re-import it once the service is healthy. For deeper diagnosis,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult).
