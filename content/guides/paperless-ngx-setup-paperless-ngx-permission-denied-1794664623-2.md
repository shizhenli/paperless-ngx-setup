---
title: "Paperless-ngx Permission Denied: Fix Docker Volume Access"
description: "Fix Paperless-ngx permission denied errors in Docker when the container cannot read the import volume or write the document store. Step-by-step checks and verification."
seo_title: "Paperless-ngx Permission Denied: Docker Volume Fix"
seo_description: "Fix Paperless-ngx permission denied errors in Docker when the container cannot read the import volume or write the document store. Step-by-step checks and verification."
keywords:
  - "paperless ngx permission denied"
date: 2026-11-21T15:17:41+08:00
publishDate: 2026-11-21T15:17:41+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You're running Paperless-ngx in Docker with a watched import volume, but uploads fail and the container cannot read the import volume or write the document store. This is a classic **paperless ngx permission denied** issue caused by UID/GID mismatches between the host and container.

## Check the container user and volume ownership

First, find the UID/GID Paperless-ngx runs as. Run `docker exec -it <container> id`. The output shows `uid=1000(paperless)` and `gid=1000(paperless)` by default. Next, check ownership of the host directories mounted for import and data: `ls -ln /path/to/import /path/to/data`. If the numeric owner differs from the container's UID/GID, the container cannot read or write those paths. That mismatch is the root cause.

## Fix ownership or configure the container user

Either change host ownership to match the container (`sudo chown -R 1000:1000 /path/to/import /path/to/data`) or set `PAPERLESS_UID` and `PAPERLESS_GID` in your compose file to match the host owner. After changing compose variables, recreate the container with `docker compose up -d`. If you change host ownership, no restart is needed, but confirm the mount is not read-only in `docker inspect`.

## Verification test

Create a test file on the host inside the import volume: `touch /path/to/import/test.pdf`. Then run `docker exec -it <container> ls -l /usr/src/paperless/consume`. If the file appears and the container can list it, permissions are fixed. Upload a small document through the web UI and confirm it moves to the document store without errors.

## Rollback note

If the container fails to start after changing UID/GID, revert the compose variables and run `docker compose up -d` again. Ownership changes on the host are reversible with your backup or previous owner values. For guided diagnosis,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult).
