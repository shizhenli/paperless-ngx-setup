---
title: "Paperless-ngx Document Stuck in Consume Queue: A Docker Watched Volume Fix"
description: "A file remains in the consume queue and never becomes searchable. Follow these Docker watched-volume checks to diagnose and recover a paperless ngx document stuck in consume."
seo_title: "Paperless-ngx Document Stuck in Consume Queue: Docker Watched Volume Fix"
seo_description: "A file remains in the consume queue and never becomes searchable. Follow these Docker watched-volume checks to diagnose and recover a paperless ngx document stuck in consume."
keywords:
  - "paperless ngx document stuck in consume"
date: 2026-11-25T08:04:17+08:00
publishDate: 2026-11-25T08:04:17+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

**paperless ngx document stuck in consume**. You have a Docker-based Paperless-ngx instance with a watched import volume, and a file sits in the consume queue without ever becoming a searchable document.

## Check the consume directory and container logs

First, confirm the file is actually visible inside the container. Run `docker exec -it <container_name> ls -la /usr/src/paperless/consume`. If the file is missing there but present on the host, your volume mapping is wrong or the file landed in the wrong subfolder. Next, inspect the consumer logs: `docker logs <container_name> 2>&1 | grep -i consume`. Look for repeated errors such as permission denied, unsupported file type, or a stalled OCR process. If the log shows the file being picked up but never finishing, the consumer worker is likely blocked.

## Verify permissions and restart the consumer

The container user must own the consume directory. Check with `docker exec -it <container_name> stat -c '%U:%G' /usr/src/paperless/consume`. If it is not the paperless user, fix ownership on the host and restart. Then restart only the consumer service: `docker compose restart webserver`. After restart, watch the queue for two minutes. If the file disappears from consume but no document appears, the issue is downstream in the task queue, not the watched volume.

## Verification test

Place a small plain-text file into the watched volume. Within one minute it should leave the consume folder and appear in Documents. If it does, the original file may have an unsupported format or corrupt structure. If it also sticks, the consumer process is not running correctly.

## Rollback and recovery

Before changing permissions or restarting, copy the stuck file out of the consume volume to a safe location. If a restart does not clear the queue, move the file back to the host, confirm the container is healthy, and re-add it once. For deeper queue inspection,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult).
