---
title: "Paperless-ngx File Path Not Found: Fix the Watched Import Folder"
description: "Troubleshoot Paperless-ngx when files in the watched import folder are ignored. Check Docker volume paths, permissions, and the consume directory to restore automatic imports."
seo_title: "Paperless-ngx File Path Not Found: Watched Folder Fix"
seo_description: "Troubleshoot Paperless-ngx when files in the watched import folder are ignored. Check Docker volume paths, permissions, and the consume directory to restore automatic imports."
keywords:
  - "paperless ngx file path not found"
date: 2026-11-18T11:10:41+08:00
publishDate: 2026-11-18T11:10:41+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You placed files in the watched import folder, but Paperless-ngx ignores them, and you are stuck before completing a controlled diagnosis.

## Confirm the container sees the folder

Open a shell inside the Paperless-ngx container and run `ls -la /usr/src/paperless/consume`. If this returns "No such file or directory," the container cannot see your host folder. Check your Docker Compose volume mapping. The left side is the host path and the right side must be `/usr/src/paperless/consume`. A typo in either path produces a **paperless ngx file path not found** condition, so the watcher has nothing to scan.

## Verify permissions and ownership

Run `id` inside the container to see the user ID. Then run `ls -ln` on the host folder. The host directory must be readable and writable by that UID. If it is owned by root with mode 755, the container can list files but cannot move or rename them, so imports silently fail. Adjust ownership with `chown -R 1000:1000` using your actual container UID.

## Check the watcher is enabled

In `docker-compose.yml`, confirm `PAPERLESS_CONSUMER_POLLING` is set only if you need polling, and that the consume volume is not mounted read-only. A read-only mount lets you see files but prevents Paperless from consuming them.

## Verification test

Create a small text file directly inside the container: `docker exec -it paperless touch /usr/src/paperless/consume/test.txt`. If it appears in the Paperless UI under Consume, the path and permissions are correct. If not, recheck the volume mapping before changing anything else.

## Rollback and recovery

If a recent Compose edit caused the failure, restore the previous `docker-compose.yml` and run `docker compose up -d`. Files already moved into the consume folder remain queued; no data is lost. If the mapping still fails after rollback,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult) with your Compose volume lines and the `ls` output.
