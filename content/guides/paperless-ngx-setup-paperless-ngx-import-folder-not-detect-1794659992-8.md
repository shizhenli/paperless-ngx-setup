---
title: "Paperless-ngx Import Folder Not Detected: Diagnose the Watched Volume"
description: "Files in your Docker watched folder are ignored? Follow these checks to confirm the volume mount, permissions, and consumer status, then verify with a test file."
seo_title: "Paperless-ngx Import Folder Not Detected: Fix the Watched Volume"
seo_description: "Files in your Docker watched folder are ignored? Follow these checks to confirm the volume mount, permissions, and consumer status, then verify with a test file."
keywords:
  - "paperless ngx import folder not detected"
date: 2026-11-17T08:07:42+08:00
publishDate: 2026-11-17T08:07:42+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You placed files in the Docker watched folder, but Paperless-ngx never picks them up, and you are stuck before running a controlled diagnosis.

## Confirm the container sees the folder

Run `docker exec -it <container> ls -la /usr/src/paperless/consume`. If the command returns an error or an empty directory while files exist on the host, the volume mount is wrong. Check your compose file for the consume volume line and confirm the host path matches the folder you are actually using. A correct result lists your files with the paperless user as owner.

## Check the consumer process

Run `docker logs <container> 2>&1 | grep -i consume`. You want a line showing the consumer started and watching the directory. If the log shows a permission error or no consumer line at all, the service is not watching. Also confirm `PAPERLESS_CONSUMER_POLLING` is set if your volume does not emit filesystem events; without it, files can sit indefinitely.

## Verify permissions

The container user must read and write the consume folder. Run `docker exec -it <container> id` and compare the UID/GID with `ls -n` on the host folder. If they differ, adjust ownership with `chown -R <uid>:<gid> <host-path>`. This is the most common reason a **paperless ngx import folder not detected** issue persists after the mount looks correct.

## Verification test

Create a small text file directly inside the host folder, wait up to one minute, then check `docker logs <container> --tail 50`. A successful consume logs the file being processed and it appears in the documents list. If nothing appears, the mount or consumer is still wrong.

## Rollback note

If a compose or permission change breaks startup, restore your previous compose file and restart the container before retrying. Keep a copy of the working configuration.

If these checks do not resolve it,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult).
