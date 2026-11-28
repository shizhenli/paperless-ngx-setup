---
title: "Paperless-ngx Permission Denied: Fix Docker Volume Ownership Before Upload"
description: "Fix paperless ngx permission denied when Docker cannot read the import volume or write the document store. Check UID/GID, volume ownership, and run a verification test."
seo_title: "Paperless-ngx Permission Denied: Docker Volume Ownership Fix"
seo_description: "Fix paperless ngx permission denied when Docker cannot read the import volume or write the document store. Check UID/GID, volume ownership, and run a verification test."
keywords:
  - "paperless ngx permission denied"
date: 2026-11-28T21:48:20+08:00
publishDate: 2026-11-28T21:48:20+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You are running Paperless-ngx in Docker with a watched import volume, and the container reports that it cannot read the import volume or write the document store, leaving you stuck at the upload or volume access step.

## Check the container user and volume ownership

First, confirm which user the container runs as. Run `docker exec -it paperless-ngx id` and note the UID and GID. Next, inspect the host directories mounted as the import volume and document store with `ls -ln /path/to/import /path/to/data`. The numeric owner and group must match the container UID/GID. If the host shows root or a different user, the container cannot read or write those paths, which produces the **paperless ngx permission denied** error.

## Interpret the result

If the import volume is owned by root but the container runs as UID 1000, the container lacks read access. If the document store is owned by a different UID, writes fail. Both conditions confirm an ownership mismatch rather than a Paperless-ngx configuration fault.

## Apply the fix

Stop the stack with `docker compose down`. Change ownership on the host: `sudo chown -R 1000:1000 /path/to/import /path/to/data`, replacing 1000 with the UID/GID from the `id` command. Keep permissions at 755 for directories and 644 for files. Start the stack again with `docker compose up -d`.

## Verification test

Place a small PDF in the import volume and watch `docker compose logs -f webserver`. A successful consume logs the document being added. Then upload a file through the web interface and confirm it appears in the document list. If either step still fails, the mount path or UID is still wrong.

## Rollback note

Before changing ownership, record the original owner with `ls -ln`. If the fix causes other services to lose access, restore ownership with `sudo chown -R original_user:original_group /path/to/import /path/to/data` and re-check the container UID.

If ownership changes do not resolve it,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult) with the `id` output and `ls -ln` results.
