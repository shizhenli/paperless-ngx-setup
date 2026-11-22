---
title: "Paperless-ngx Import Folder Not Detected: A Controlled Diagnosis"
description: "Troubleshoot why Paperless-ngx ignores files in your Docker watched import folder. Step-by-step checks for volume mounts, permissions, and container detection."
seo_title: "Paperless-ngx Import Folder Not Detected: Fix Docker Watched Volume"
seo_description: "Troubleshoot why Paperless-ngx ignores files in your Docker watched import folder. Step-by-step checks for volume mounts, permissions, and container detection."
keywords:
  - "paperless ngx import folder not detected"
date: 2026-11-22T08:04:19+08:00
publishDate: 2026-11-22T08:04:19+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You placed files in the watched import folder, but Paperless-ngx ignores them, leaving you stuck before any controlled diagnosis. This article walks through a focused check to confirm whether the container can actually see the folder.

## Verify the volume mount inside the container

First, confirm the import folder is mounted at the expected path. Run:

```
docker exec -it <container_name> ls -la /usr/src/paperless/consume
```

Interpretation: if you see your files, the mount exists. If the directory is empty or missing, the volume mapping in docker-compose.yml is wrong. Check that the host path and container path match your configuration, then restart the container.

## Check file permissions and ownership

Paperless-ngx runs as a specific user (often UID 1000). Files must be readable by that user. Run:

```
docker exec -it <container_name> stat /usr/src/paperless/consume/yourfile.pdf
```

If ownership shows root:root and permissions deny others, adjust with `chown -R 1000:1000` on the host folder. This is a common reason the **paperless ngx import folder not detected** symptom appears even when the mount is correct.

## Confirm the watcher is active

Check container logs for consume events:

```
docker logs <container_name> | grep -i consume
```

You should see polling or inotify messages. If nothing appears, the consumer may be disabled or the path misconfigured in `PAPERLESS_CONSUMER_POLLING` settings.

## Verification test

Create a small test file directly inside the mounted folder:

```
docker exec -it <container_name> touch /usr/src/paperless/consume/test.txt
```

Then watch logs for a new consume task. If the file is picked up, the folder is detected. If not, revisit the mount and permission steps.

## Rollback note

If changes to docker-compose.yml break startup, revert to your previous volume mapping and restart. Keep a backup of the compose file before editing.

If the folder still isn't detected after these checks,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult) with your compose file and logs.
