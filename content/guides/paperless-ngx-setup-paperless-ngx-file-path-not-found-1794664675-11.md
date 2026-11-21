---
title: "Paperless-ngx File Path Not Found: When the Watched Import Folder Is Ignored"
description: "Troubleshoot Paperless-ngx when files in a Docker watched import folder are ignored because the container cannot see the host path. Step-by-step checks, verification, and recovery."
seo_title: "Paperless-ngx File Path Not Found: Fix Watched Import Folder Not Detected"
seo_description: "Troubleshoot Paperless-ngx when files in a Docker watched import folder are ignored because the container cannot see the host path. Step-by-step checks, verification, and recovery."
keywords:
  - "paperless ngx file path not found"
date: 2026-11-21T15:23:52+08:00
publishDate: 2026-11-21T15:23:52+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You placed files in the watched import folder, but Paperless-ngx in Docker never picks them up, and you are stuck at the watched import folder is not detected step before a controlled diagnosis is complete.

## Confirm the container sees the path

Open a shell in the Paperless-ngx container and list the configured consume directory, for example `ls -la /usr/src/paperless/consume`. If the command returns "No such file or directory" or an empty directory while your host folder has files, the volume mapping is wrong. That is the classic **paperless ngx file path not found** symptom: the host path exists, but the container path does not.

## Check the compose mapping and permissions

In `docker-compose.yml`, verify the consume volume line maps your host folder to the exact container path Paperless expects, such as `./consume:/usr/src/paperless/consume`. A typo on either side, a trailing slash mismatch, or a relative path resolved from the wrong directory will break detection. Then check ownership: the container user must be able to read the mounted folder. Run `docker compose config` to see the resolved paths, and compare them with `docker inspect` on the running container.

## Verification test

Place a single small PDF in the host watched folder, then run `docker compose exec webserver ls -la /usr/src/paperless/consume`. If the file appears there, the path is correct; if it does not, fix the mapping before changing anything else. Watch the logs with `docker compose logs -f webserver` for consume messages.

## Rollback and recovery

If a compose edit makes things worse, restore the previous `docker-compose.yml` from backup and run `docker compose up -d` to return to the last known state. Keep the test PDF out of the folder until detection works, so you do not create duplicate imports. If the mapping still fails after these checks,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult).
