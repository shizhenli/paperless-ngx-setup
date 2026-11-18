---
title: "Paperless-ngx Consumer Service Restart: When a File Stays in the Consume Queue"
description: "A file remains in the consume queue and never becomes searchable in Docker. Learn how to diagnose and use a paperless ngx consumer service restart to clear the stuck queue."
seo_title: "Paperless-ngx Consumer Service Restart: Fix Stuck Consume Queue"
seo_description: "A file remains in the consume queue and never becomes searchable in Docker. Learn how to diagnose and use a paperless ngx consumer service restart to clear the stuck queue."
keywords:
  - "paperless ngx consumer service restart"
date: 2026-11-18T09:04:14+08:00
publishDate: 2026-11-18T09:04:14+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You uploaded a file to your watched import volume, but it is still sitting in the consume queue and never becomes a searchable document.

## Check the Consumer Container Status

Run `docker ps` and look for the consumer container. If it is not listed or shows `Exited`, the consumer is not running. Check its logs with `docker logs <consumer_container_name> --tail 50`. Look for errors like `Permission denied`, `No such file or directory`, or repeated tracebacks. If the logs show a clean startup but no processing activity, the consumer may be idle or stuck on a lock. Note the exact error message; that tells you whether this is a permissions issue, a missing volume, or a crashed process.

## Confirm the Queue and Volume

Inspect the consume directory inside the container: `docker exec <consumer_container_name> ls -la /usr/src/paperless/consume`. If your file is present but not moving, the consumer is not picking it up. Also check that the host path is correctly mounted by comparing `docker inspect <consumer_container_name> | grep -A5 Mounts` with your compose file. A mismatch here means the consumer sees an empty folder.

## Restart the Consumer

If the logs show a crash or the container is stopped, perform a **paperless ngx consumer service restart** with `docker restart <consumer_container_name>`. Wait 30 seconds, then check the logs again for processing messages.

## Verification Test

Place a small text file in the consume volume. Within one minute, it should disappear from the queue and appear in the documents list. If it does not, the restart did not resolve the underlying issue.

## Rollback and Recovery

If the restart causes repeated crashes, stop the container and review the last error. Revert any recent compose changes before retrying. For persistent queue stalls,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult) with your logs and compose file.
