---
title: "Paperless-ngx Permission Denied: Diagnose Docker Volume Access Before You Change Anything"
description: "Troubleshoot Paperless-ngx permission denied errors in Docker when the container cannot read the import volume or write the document store. Includes exact checks, result interpretation, a verification test, and a rollback note."
seo_title: "Paperless-ngx Permission Denied: Fix Docker Volume Access"
seo_description: "Troubleshoot Paperless-ngx permission denied errors in Docker when the container cannot read the import volume or write the document store. Includes exact checks, result interpretation, a verification test, and a rollback note."
keywords:
  - "paperless ngx permission denied"
date: 2026-11-22T09:04:14+08:00
publishDate: 2026-11-22T09:04:14+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

## Troubleshooting

You uploaded a file to the watched import volume, but Paperless-ngx reports that access is denied before any controlled diagnosis has run. ## Check the container identity first Run `docker exec -it <container> id` and note the UID and GID. Then run `docker exec -it <container> ls -ld /usr/src/paperless/consume` and `ls -ld /usr/src/paperless/media`. If the directory owner or group does not match the container UID/GID, the container cannot read the import volume or write the document store. ## Inspect the host mount permissions On the host, run `ls -ld /path/to/consume` and `ls -ld /path/to/media`. If the owner is root and the mode is 755, a non-root container user cannot write. If the mode is 700 and the container user is different, it cannot read. Compare the numeric owner to the container UID/GID from the first check. ## Fix ownership deliberately Choose one approach: set the host directory owner to the container UID/GID with `chown -R <uid>:<gid> /path/to/consume /path/to/media`, or set `PAPERLESS_UID` and `PAPERLESS_GID` in the compose environment to match the host owner, then recreate the container. Do not use 777 as a permanent fix. ## Verification test Create a small test file on the host inside the consume volume, then run `docker exec -it <container> touch /usr/src/paperless/consume/.write-test`. If the touch succeeds and the file appears on the host, volume access is restored. Remove the test file afterward. ## Rollback note If the change breaks startup, restore the previous ownership with `chown -R <old-owner>:<old-group>` and revert the compose environment before restarting. If the **paperless ngx permission denied** message persists after ownership and UID/GID match, stop changing permissions and

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult) with the output of the id and ls commands.
