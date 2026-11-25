---
title: "Paperless-ngx Watch Folder Not Importing: Diagnose the Consume Volume"
description: "Files in your Paperless-ngx watched folder are ignored? Follow these checks to confirm the consume volume is mounted and recognized, then verify with a test file."
seo_title: "Paperless-ngx Watch Folder Not Importing: Fix the Consume Volume"
seo_description: "Files in your Paperless-ngx watched folder are ignored? Follow these checks to confirm the consume volume is mounted and recognized, then verify with a test file."
keywords:
  - "paperless ngx watch folder not importing"
date: 2026-11-25T09:04:13+08:00
publishDate: 2026-11-25T09:04:13+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You placed files in the watched import folder, but Paperless-ngx never picks them up, and you are stuck before running a controlled diagnosis.

## Confirm the consume directory inside the container

Open a shell in the Paperless container and list the consume path. If the directory is missing or empty while your host folder has files, the volume is not mounted where Paperless expects it. Check your compose file for a line like `- ./consume:/usr/src/paperless/consume`. The right side must match `PAPERLESS_CONSUME_DIR` or the default consume path. If the left side points to a different host folder, fix it and recreate the container with `docker compose up -d`.

## Check file visibility and permissions

From inside the container, run `ls -la` on the consume directory. Files should appear with the same names you see on the host. If they are absent, the mount is wrong. If they appear but Paperless still ignores them, confirm the container user can read them. A quick test is `docker exec -it paperless touch /usr/src/paperless/consume/test.txt`; a permission error means the UID mapping is off. Align `USERMAP_UID` and `USERMAP_GID` with the host folder owner, then restart.

## Verify the consumer is running

Check `docker compose logs paperless` for consume-related errors. A healthy start mentions watching the consume directory. If the service exited or is stuck, restart it. This is the point where most cases of **paperless ngx watch folder not importing** are resolved by correcting the mount or permissions rather than changing settings.

## Run a verification test

Copy one small PDF into the host consume folder. Within a minute, the file should disappear from consume and a document should appear in Paperless. If it stays put, recheck the mount path and container logs.

## Rollback note

If a compose edit breaks startup, restore the previous volume line and recreate the container. Your original files remain on the host.

Still stuck after these checks?

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult).
