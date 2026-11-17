---
title: "Paperless-ngx Docker Consume Queue Stuck: Diagnose the Watched Volume Before It Clears"
description: "A file remains in the Paperless-ngx consume queue and never becomes searchable. Diagnose the Docker watched volume, permissions, and consumer logs to restore processing."
seo_title: "Paperless-ngx Document Stuck in Consume: Docker Watched Volume Fix"
seo_description: "A file remains in the Paperless-ngx consume queue and never becomes searchable. Diagnose the Docker watched volume, permissions, and consumer logs to restore processing."
keywords:
  - "paperless ngx document stuck in consume"
date: 2026-11-17T12:04:07+08:00
publishDate: 2026-11-17T12:04:07+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You are running Paperless-ngx in Docker with a watched import volume, and a file remains in the consume queue without ever becoming a searchable document. This is a common **paperless ngx document stuck in consume** situation, and it is usually caused by the consumer container not seeing or being able to read the file.

## Check the watched volume mount

First, confirm the file is actually inside the container's consume directory. Run `docker exec -it <consumer_container> ls -la /usr/src/paperless/consume`. If the file is missing, your Docker volume mapping is wrong: the host path in `docker-compose.yml` must point to the folder where the file was dropped. If the file is present but owned by root or another user, the consumer cannot read it. Check with `docker exec -it <consumer_container> id` and compare the UID/GID to the file owner. A mismatch means you need to correct ownership on the host or set `PAPERLESS_UID` and `PAPERLESS_GID` to match.

## Read the consumer logs

Run `docker logs --tail 100 <consumer_container>`. Look for permission denied, file not found, or OCR errors. If the log shows the file was detected but processing never finishes, the consumer may be waiting on a locked file or a failed OCR step. Restart the consumer container with `docker restart <consumer_container>` and watch the logs again. A clean restart often clears a transient lock.

## Verification test

Create a small plain-text file in the watched folder and wait up to two minutes. If it appears in Paperless-ngx as a document, the queue is working and the original file likely had a permission or format issue. If the test file also stalls, the volume mount or container permissions are still wrong.

## Rollback and recovery

Before changing ownership or compose settings, note the current UID/GID and volume paths so you can revert. If a restart makes things worse, restore the previous `docker-compose.yml` and restart the stack. For persistent cases,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult) with your logs and compose file.
