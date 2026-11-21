---
title: "Paperless-ngx Upload Permission Denied: Fix the Import Volume"
description: "Troubleshoot Paperless-ngx upload permission denied in Docker by checking UID/GID ownership, volume mounts, and write access to the import and document store."
seo_title: "Paperless-ngx Upload Permission Denied: Fix the Import Volume"
seo_description: "Troubleshoot Paperless-ngx upload permission denied in Docker by checking UID/GID ownership, volume mounts, and write access to the import and document store."
keywords:
  - "paperless ngx upload permission denied"
date: 2026-11-21T15:20:46+08:00
publishDate: 2026-11-21T15:20:46+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You are running Paperless-ngx in Docker with a watched import volume, and the container cannot read the import volume or write the document store, leaving you at a **paperless ngx upload permission denied** error before you can complete a controlled diagnosis.

## Check ownership and IDs

First, confirm the container user. Run `docker exec -it paperless-ngx id` and note the UID and GID. Next, inspect the host mount: `ls -ln /path/to/import` and `ls -ln /path/to/media`. The numeric owner and group must match the container's UID and GID, and the import directory needs read and execute for that user. If the host shows root:root while the container runs as 1000:1000, the container cannot traverse the volume. That is the failure.

## Check mount and write access

Run `docker inspect paperless-ngx` and verify the import and media paths are bind-mounted to the expected host directories, not an empty named volume. Then test write access inside the container: `docker exec -it paperless-ngx touch /usr/src/paperless/media/testfile`. A permission denied result confirms the document store is not writable. For the import volume, run `docker exec -it paperless-ngx ls /usr/src/paperless/consume` and check for errors.

## Verification test

Correct ownership on the host with `chown -R 1000:1000 /path/to/import /path/to/media` and `chmod -R u+rwX` on both. Restart the container, drop a small PDF into the import folder, and watch the logs. A successful consume and a new document entry confirm the fix.

## Rollback note

If you changed ownership broadly, record the original owner with `stat` before editing so you can restore it if another service depends on those files. If the issue persists after ownership and mount checks,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult) with your `id` output and mount inspection results.
