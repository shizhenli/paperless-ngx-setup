---
title: "Paperless-ngx Permission Denied: Diagnose Docker Volume Access"
description: "Troubleshoot Paperless-ngx permission denied errors in Docker when the container cannot read the import volume or write the document store. Step-by-step checks and verification."
seo_title: "Paperless-ngx Permission Denied: Fix Docker Import Volume Access"
seo_description: "Troubleshoot Paperless-ngx permission denied errors in Docker when the container cannot read the import volume or write the document store. Step-by-step checks and verification."
keywords:
  - "paperless ngx permission denied"
date: 2026-11-24T10:04:14+08:00
publishDate: 2026-11-24T10:04:14+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You're running Paperless-ngx in Docker with a watched import volume, but uploads fail and the container reports it cannot read the import volume or write the document store.

## Check container user and volume ownership

First, find the user ID Paperless-ngx runs as inside the container. Run `docker exec -it paperless-ngx id` and note the UID and GID. Then inspect the host directories mounted as the import volume and document store with `ls -ln /path/to/import` and `ls -ln /path/to/media`. The numeric owner and group must match the container's UID and GID. If they differ, the container cannot read or write those paths, which produces the **paperless ngx permission denied** error.

## Interpret the results

If the import volume is owned by root but the container runs as UID 1000, that is the cause. If the document store is writable but the import volume is not, only imports fail. If both are misowned, uploads and document storage both fail. Correct ownership with `sudo chown -R 1000:1000 /path/to/import /path/to/media`, replacing 1000 with the actual UID and GID from the `id` command.

## Verification test

Restart the container with `docker compose restart paperless-ngx`. Then place a small test file in the import volume and watch the logs with `docker compose logs -f paperless-ngx`. A successful consume shows the file being processed and moved to the document store. You can also run `docker exec -it paperless-ngx touch /usr/src/paperless/import/testfile` to confirm write access.

## Rollback and recovery

If ownership changes cause other services to lose access, note the original owner with `stat -c '%u:%g' /path/to/import` before changing it, and restore with `chown` if needed. Avoid recursive changes on shared volumes without recording the previous state.

If the checks still fail,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult) with the output of the `id` and `ls -ln` commands.
