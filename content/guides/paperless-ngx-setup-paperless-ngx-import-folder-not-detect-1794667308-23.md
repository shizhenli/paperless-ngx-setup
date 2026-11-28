---
title: "Paperless-ngx Import Folder Not Detected: Why Files Are Ignored and How to Fix It"
description: "Troubleshoot why Paperless-ngx ignores files in your Docker watched import folder. Step-by-step checks, verification test, and rollback note."
seo_title: "Paperless-ngx Import Folder Not Detected: Fix Watched Volume"
seo_description: "Troubleshoot why Paperless-ngx ignores files in your Docker watched import folder. Step-by-step checks, verification test, and rollback note."
keywords:
  - "paperless ngx import folder not detected"
date: 2026-11-28T21:48:20+08:00
publishDate: 2026-11-28T21:48:20+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You placed files in the watched import folder, but Paperless-ngx never picks them up, and you are stuck before running a controlled diagnosis. Here is how to isolate the failure without guessing.

## Confirm the container sees the folder

Run `docker exec -it <container> ls -la /usr/src/paperless/consume`. If the folder is missing or empty while your host shows files, the bind mount is wrong. Check your `docker-compose.yml`: the host path must map to `/usr/src/paperless/consume`. A common mistake is mapping to `/consume` or using a relative path. After editing, run `docker compose up -d` to recreate the container.

## Check file ownership and permissions

The container user (often UID 1000) must read and write the folder. On the host, run `ls -ln` on the folder. If files are owned by root or another UID, the consumer cannot read them. Fix with `sudo chown -R 1000:1000 /path/to/consume` and `chmod -R 755`. Then restart the container.

## Verify the consumer is running

Run `docker logs <container> | grep consume`. You should see lines about watching the directory. If the consumer is stopped, check for errors about the path. Also confirm `PAPERLESS_CONSUMER_POLLING` is not set to an excessively long interval; default is fine.

## Verification test

Create a small text file on the host: `echo test > /path/to/consume/test.txt`. Within a minute, check the Paperless-ngx dashboard for a new document. If it appears, the pipeline works. If not, re-check the mount and permissions.

## Rollback note

If changes break startup, revert your `docker-compose.yml` to the previous version and run `docker compose up -d`. Your original documents remain safe in the data volume.

If the **paperless ngx import folder not detected** issue persists after these checks,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult) with your compose file and logs.
