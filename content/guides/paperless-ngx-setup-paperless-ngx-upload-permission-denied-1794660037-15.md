---
title: "Paperless-ngx Upload Permission Denied: Fix Container Read and Write Access"
description: "Fix Paperless-ngx upload permission denied errors in Docker by checking UID/GID ownership, volume mounts, and write access to the document store, then verify with a test file."
seo_title: "Paperless-ngx Upload Permission Denied: Docker Volume Fix"
seo_description: "Fix Paperless-ngx upload permission denied errors in Docker by checking UID/GID ownership, volume mounts, and write access to the document store, then verify with a test file."
keywords:
  - "paperless ngx upload permission denied"
date: 2026-11-18T08:04:21+08:00
publishDate: 2026-11-18T08:04:21+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You are running Paperless-ngx in Docker with a watched import volume, and the container reports that it cannot read the import volume or write the document store.

## Check ownership and mount paths

First, confirm the container user. Run `docker exec -it <container> id` and note the UID and GID, typically 1000. On the host, run `ls -ln /path/to/import` and `ls -ln /path/to/data`. The numeric owner of both directories should match the container UID and GID. If they differ, the container cannot read incoming files or write consumed documents, which produces the **paperless ngx upload permission denied** error.

Next, verify the mount is not read-only. Run `docker inspect <container> --format '{{json .Mounts}}'` and check that the import and data volumes show `"RW": true`. A read-only mount will block writes even when ownership is correct.

## Apply the fix

Correct ownership on the host with `sudo chown -R 1000:1000 /path/to/import /path/to/data`, replacing 1000 with the UID and GID from the container. If your setup uses a compose file, confirm the volume paths match the host directories exactly. Then restart the container with `docker compose restart`.

## Verification test

Place a small PDF into the import volume and watch the logs with `docker logs -f <container>`. A successful consume shows the document moving into the store without permission errors. You can also run `docker exec -it <container> touch /usr/src/paperless/data/.write-test` and confirm it succeeds.

## Rollback note

If the change causes other services to lose access, restore the previous ownership with `sudo chown -R <old-uid>:<old-gid> /path/to/import /path/to/data` and recheck the compose user mapping before retrying.

If the error persists after these checks,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult) with your container ID output and mount inspection results.
