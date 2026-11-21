---
title: "Watched Import Folder Not Detected in Paperless-ngx Docker: A Practical Diagnosis"
description: "Files placed in the watched import folder are ignored by Paperless-ngx in Docker. Follow these checks to diagnose why the watched volume is not detected and restore automatic imports."
seo_title: "Paperless-ngx Import Folder Not Detected: Fix the Watched Volume"
seo_description: "Files placed in the watched import folder are ignored by Paperless-ngx in Docker. Follow these checks to diagnose why the watched volume is not detected and restore automatic imports."
keywords:
  - "paperless ngx import folder not detected"
date: 2026-11-21T15:17:41+08:00
publishDate: 2026-11-21T15:17:41+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You placed files in the watched import folder, but Paperless-ngx in Docker never picks them up.  

## Confirm the container sees the folder

Run `docker exec -it <container> ls -la /usr/src/paperless/consume` (or your configured consume path). If the command returns 'No such file or directory', the volume is not mounted at the expected path. Check your `docker-compose.yml` for a volume line like `./consume:/usr/src/paperless/consume`. A missing or misspelled host path means the container has an empty directory. If `ls` shows an empty folder, verify the host directory contains your test file and that the mount is not read-only.

## Check permissions and ownership

Inside the container, run `id` to see the Paperless user (often UID 1000). Then `ls -ln /usr/src/paperless/consume` on the host. The files must be readable by that UID. If they are owned by root with mode 600, Paperless cannot read them. Fix with `sudo chown -R 1000:1000 ./consume` and `chmod -R 755 ./consume`. This is a common reason the **paperless ngx import folder not detected** symptom appears even when the mount exists.

## Verify the consumer is running

Check `docker logs <container> | grep consume`. You should see a line like 'Consuming /usr/src/paperless/consume'. If not, the consumer service may be disabled or crashed. Restart the container and watch logs for errors about the consume directory.

## Verification test

Create a small text file on the host inside the watched folder: `echo test > ./consume/test.txt`. Within a minute, check the Paperless dashboard for a new document. If it appears, the folder is detected. If not, re-check the mount path and permissions.

## Rollback note

If you changed volume paths or permissions and imports still fail, revert to your last working `docker-compose.yml` and restart. Keep a backup of the compose file before editing.

If these steps do not resolve it,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult) with your compose file and container logs.
