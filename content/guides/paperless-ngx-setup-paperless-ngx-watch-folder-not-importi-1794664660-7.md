---
title: "Paperless-ngx Watch Folder Not Importing: Diagnose the Watched Volume"
description: "Troubleshoot a Paperless-ngx Docker watch folder that ignores files. Check volume mounts, permissions, and consumer logs to restore automatic importing."
seo_title: "Paperless-ngx Watch Folder Not Importing: Fix the Watched Volume"
seo_description: "Troubleshoot a Paperless-ngx Docker watch folder that ignores files. Check volume mounts, permissions, and consumer logs to restore automatic importing."
keywords:
  - "paperless ngx watch folder not importing"
date: 2026-11-21T15:20:46+08:00
publishDate: 2026-11-21T15:20:46+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You placed a PDF in the watched import folder, but Paperless-ngx never picks it up, and you are stuck before running a controlled diagnosis.

## Confirm the container sees the folder

Open a shell in the Paperless container and list the watched path, for example `docker exec -it paperless-ngx ls -la /usr/src/paperless/consume`. If the file is missing, your Docker volume mapping is wrong. Compare the host path in `docker-compose.yml` with the container path; the left side must point to the real host directory. If the file appears, the mount is fine and the problem is inside Paperless.

## Check permissions and ownership

The consumer runs as a specific user, usually UID 1000. Run `docker exec -it paperless-ngx id` and compare it with `ls -ln` on the watched folder. If the file is owned by root or another UID, the consumer cannot read it. Fix ownership on the host with `sudo chown -R 1000:1000 /path/to/consume`, then restart the container. This is the most common cause of a **paperless ngx watch folder not importing**.

## Read the consumer logs

Run `docker logs paperless-ngx --tail 100` and look for lines mentioning the consume directory. A message such as permission denied or no such file confirms the earlier checks. Silence means the watcher is not polling; verify `PAPERLESS_CONSUMER_POLLING` is set correctly if you use a network mount.

## Verification test

Create a small text file inside the watched folder and watch the logs. A successful import logs the document name and moves the file out of the consume directory. If it stays put, repeat the ownership check.

## Rollback note

If a recent compose change caused this, restore the previous volume mapping and restart. Keep a copy of the original file before changing permissions. For hands-on diagnosis,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult).
