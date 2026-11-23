---
title: "Paperless-ngx Document Stuck in Consume: A Docker Watched Volume Diagnosis"
description: "Troubleshoot a Paperless-ngx document stuck in the consume queue in Docker with a watched import volume. Step-by-step checks, verification, and rollback."
seo_title: "Paperless-ngx Document Stuck in Consume: Docker Watched Volume Fix"
seo_description: "Troubleshoot a Paperless-ngx document stuck in the consume queue in Docker with a watched import volume. Step-by-step checks, verification, and rollback."
keywords:
  - "paperless ngx document stuck in consume"
date: 2026-11-23T08:04:14+08:00
publishDate: 2026-11-23T08:04:14+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You're running Paperless-ngx in Docker with a watched import volume, and a file remains in the consume queue, never becoming a searchable document.

## Check the consume service logs

Run `docker logs --tail 100 <container_name>` (replace with your Paperless-ngx container). Look for lines mentioning the file name. If you see repeated "Waiting for file to be stable" or "File is still being written" messages, the watcher believes the file is incomplete. Confirm the file size in the import volume is not changing: `docker exec <container_name> stat /usr/src/paperless/consume/<filename>`. If size and mtime are stable for over a minute, the watcher should proceed—if not, something is still writing to the file.

## Verify permissions and volume mapping

Inside the container, run `ls -l /usr/src/paperless/consume`. The file must be readable by the paperless user (UID 1000 by default). If ownership is root or another UID, fix it on the host: `sudo chown 1000:1000 <host_path>/<filename>`. Also confirm your compose file maps the host directory to `/usr/src/paperless/consume` exactly—a typo here means the watcher sees an empty folder.

## Test with a known-good file

Copy a simple text-based PDF into the watched volume. Watch the logs for successful consumption. If that file processes but yours doesn't, the original may be corrupt or locked. This isolates whether the issue is the **paperless ngx document stuck in consume** or the volume itself.

## Rollback and recovery

If you changed permissions or mappings, revert them before retrying. To clear a stuck entry, stop the container, move the file out of the consume volume, restart, then place it back. This forces a fresh queue entry without losing the original.

If the file still won't move after these checks,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult) with your logs and compose file.
