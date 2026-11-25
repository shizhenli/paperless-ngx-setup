---
title: "Paperless-ngx Upload Permission Denied: Diagnose Volume Access Before Changing Permissions"
description: "Troubleshoot paperless ngx upload permission denied in Docker by checking UID/GID ownership, mount paths, and write access to the import and document volumes, then verify with a controlled test."
seo_title: "Paperless-ngx Upload Permission Denied: Fix Docker Volume Access"
seo_description: "Troubleshoot paperless ngx upload permission denied in Docker by checking UID/GID ownership, mount paths, and write access to the import and document volumes, then verify with a controlled test."
keywords:
  - "paperless ngx upload permission denied"
date: 2026-11-25T10:04:11+08:00
publishDate: 2026-11-25T10:04:11+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You are running Paperless-ngx in Docker with a watched import volume, and the container cannot read the import folder or write to the document store, so uploads fail with access denied.

## Check ownership and IDs first

On the host, run `ls -ln /path/to/import` and `ls -ln /path/to/media`. Note the numeric owner and group. Then run `docker exec -it paperless-ngx id`. Compare the container user's UID/GID with the volume owners. If they differ, the container cannot read or write those paths. Also run `docker inspect paperless-ngx` and confirm the import and media bind mounts point to the expected host paths, not an empty or wrong directory.

## Interpret the results

If the import folder is owned by root but the container runs as UID 1000, reads fail. If the media folder is owned by UID 1000 but the container runs as another UID, writes fail. If the mount path inside the container does not match the configured import or media path, the container sees an empty directory and permission errors follow. This is the common cause of **paperless ngx upload permission denied**.

## Apply the minimal fix

Set the container to run with the same UID/GID as the volume owner using the PUID and PGID environment variables, or change ownership on the host with `chown -R 1000:1000 /path/to/import /path/to/media`. Keep permissions at 755 for directories and 644 for files unless your setup requires otherwise. Restart the container after changes.

## Verification test

Create a small text file on the host inside the import folder, wait for the consumer to run, and confirm the document appears in Paperless-ngx. Then upload a file through the web interface and confirm it saves without an access error.

## Rollback note

If the change breaks other services, restore the previous ownership with your recorded UID/GID values and restart. If the cause remains unclear,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult) before making broader permission changes.
