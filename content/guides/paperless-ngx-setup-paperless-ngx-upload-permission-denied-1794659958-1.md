---
title: "Paperless-ngx Upload Permission Denied: Fix Docker Volume Access"
description: "Resolve paperless ngx upload permission denied in Docker by checking volume ownership, container user, and write access to the document store."
seo_title: "Paperless-ngx Upload Permission Denied: Docker Volume Fix"
seo_description: "Resolve paperless ngx upload permission denied in Docker by checking volume ownership, container user, and write access to the document store."
keywords:
  - "paperless ngx upload permission denied"
date: 2026-11-15T18:51:33+08:00
publishDate: 2026-11-15T18:51:33+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You're running Paperless-ngx in Docker with a watched import volume, and the container can't read the import volume or write the document store, leaving you with a **paperless ngx upload permission denied** error.

## Check Volume Ownership and Container User

First, identify the user the container runs as. Run `docker exec -it <container_name> id`. Note the UID and GID. Next, check ownership of the host directories mounted as the import volume and document store: `ls -ln /path/to/import /path/to/media`. The numeric owner must match the container's UID/GID. If they differ, the container cannot read or write. Also confirm the directories are not mounted read-only by inspecting `docker inspect <container_name>` and looking for `"RW": false` in the Mounts section.

## Fix Permissions

If ownership mismatches, change it on the host: `sudo chown -R <UID>:<GID> /path/to/import /path/to/media`. If the container user is not root and you prefer group access, set the directories to group-writable and add the container user to that group. Avoid `chmod 777`; use `chmod 775` for directories and `664` for files. After changes, restart the container: `docker restart <container_name>`.

## Verification Test

Create a test file in the import volume from the host, then check the container can see it: `docker exec -it <container_name> ls -l /usr/src/paperless/consume`. If the file appears, the read path works. Then test write access to the document store: `docker exec -it <container_name> touch /usr/src/paperless/media/testfile`. A successful touch means write access is restored. Remove the test file afterward.

## Rollback and Recovery

If permissions break other services, revert ownership to the previous values you recorded. Keep a note of original UID/GID before changes. For persistent issues, stop the container, correct ownership, and start it again rather than editing files inside the container.

If the checks still fail,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult) with your `docker inspect` output and directory listings.
