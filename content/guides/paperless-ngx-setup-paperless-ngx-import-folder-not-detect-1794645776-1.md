---
title: "Paperless-ngx Import Folder Not Detected: Fix the Watched Volume"
description: "Files placed in your Docker watched import folder are ignored by Paperless-ngx. Follow these checks to confirm volume mounts, permissions, and consumer status, then verify with a test file."
seo_title: "Paperless-ngx Import Folder Not Detected: Docker Watched Volume Fix"
seo_description: "Files placed in your Docker watched import folder are ignored by Paperless-ngx. Follow these checks to confirm volume mounts, permissions, and consumer status, then verify with a test file."
keywords:
  - "paperless ngx import folder not detected"
date: 2026-11-14T19:32:27+08:00
publishDate: 2026-11-14T19:32:27+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You placed files in the watched import folder, but Paperless-ngx ignores them and the folder appears undetected. Start by confirming the container sees the same path you are writing to. Run `docker exec -it <container> ls -la /usr/src/paperless/consume` and compare the output with your host folder. If the listing is empty while your host folder has files, the Docker volume mapping is wrong or points to a different directory. Check `docker inspect <container>` for the consume mount and confirm the host path and container path match your compose file.

## Check permissions and consumer status

Next, verify the container user can read the folder. The listing should show the files and the directory should be readable by the paperless user. If permissions are denied, adjust ownership on the host to match the container UID. Then confirm the consumer process is running with `docker logs <container> | grep -i consume`. A healthy start shows the consumer watching the consume directory. If it exited or reports a missing path, restart the container after correcting the mount.

## Verification test

Create a small text file directly inside the host watched folder, wait up to a minute, then check the Paperless-ngx dashboard for a new document. If it appears, the **paperless ngx import folder not detected** issue is resolved. If not, repeat the `ls` check inside the container to confirm the test file is visible there.

## Rollback and recovery

If changes make things worse, restore your previous compose volume mapping and restart the container. Keep a copy of the original compose file before editing. For guided diagnosis,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult).
