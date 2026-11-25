---
title: "Paperless-ngx File Path Not Found: Why Your Watched Folder Is Ignored"
description: "Troubleshoot Paperless-ngx when files in a Docker watched import folder are ignored. Check volume mounts, paths, and permissions with a simple verification test."
seo_title: "Paperless-ngx File Path Not Found: Fix Watched Folder Ignored"
seo_description: "Troubleshoot Paperless-ngx when files in a Docker watched import folder are ignored. Check volume mounts, paths, and permissions with a simple verification test."
keywords:
  - "paperless ngx file path not found"
date: 2026-11-25T12:04:16+08:00
publishDate: 2026-11-25T12:04:16+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

## Troubleshooting

You placed files in the watched import folder, but Paperless-ngx ignores them, and now you need to confirm whether the container can actually see that path. Start by checking the container's view of the watched folder. Run `docker exec -it paperless-ngx ls -la /usr/src/paperless/consume` (adjust the path to match your `PAPERLESS_CONSUME_DIR`). If the command returns “No such file or directory,” the host folder is not mounted at the expected path—this is the classic **paperless ngx file path not found** symptom. Next, compare the host folder path in your `docker-compose.yml` volume mapping with the container path. The left side is the host; the right side must match the consume directory exactly. A trailing slash or typo in either side silently breaks detection. Also verify permissions: the container user (often UID 1000) must be able to read and list the folder. Run `docker exec -it paperless-ngx id` and check that the UID owns or can access the mounted directory. If permissions are wrong, files may exist but remain invisible to the consumer. For a verification test, create a small text file directly on the host inside the watched folder, then run `docker exec -it paperless-ngx ls -la /usr/src/paperless/consume` again. If the file appears, the mount is correct and the issue is likely file ownership or extension filtering. If it does not appear, fix the volume mapping and restart the container with `docker compose down && docker compose up -d`. As a rollback, remove the test file and restore your previous compose file if the new mapping causes startup errors. If the path still cannot be resolved,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult) before changing more settings.
