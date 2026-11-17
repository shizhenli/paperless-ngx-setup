---
title: "Paperless-ngx Duplicate Documents: Why Re-imports Create New Records"
description: "Troubleshoot paperless ngx duplicate documents when re-importing the same file into a Docker watched volume. Check checksums, consumer settings, and verify the fix."
seo_title: "Paperless-ngx Duplicate Documents: Fix Re-import Deduplication"
seo_description: "Troubleshoot paperless ngx duplicate documents when re-importing the same file into a Docker watched volume. Check checksums, consumer settings, and verify the fix."
keywords:
  - "paperless ngx duplicate documents"
date: 2026-11-17T10:04:07+08:00
publishDate: 2026-11-17T10:04:07+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You re-import a file into your Docker watched volume and Paperless-ngx creates a second record instead of recognizing the original.

## Check the stored checksum first
Open the original document in Paperless-ngx and note its checksum under the document details. Then run `sha256sum` on the file you are re-importing, inside the container if needed: `docker exec -it paperless-ngx sha256sum /usr/src/paperless/consume/yourfile.pdf`. If the two hashes differ, the files are not byte-identical and deduplication cannot match them. Re-export or copy the original file rather than regenerating it.

## Confirm the consumer deduplication setting
In `docker-compose.yml` or `docker-compose.env`, check that `PAPERLESS_CONSUMER_DEDUPLICATE_ON_REIMPORT` is set to `true` (or left unset, since the default is enabled). After any change, recreate the container with `docker compose up -d`. If this value is `false`, every re-import becomes a new record.

## Inspect the consume log
Run `docker compose logs -f webserver` and watch a test import. A duplicate warning such as "Document already exists" confirms deduplication is active; silence followed by a new task ID means the check was skipped. This is the point where most **paperless ngx duplicate documents** cases become clear.

## Verification test
Import a small PDF, wait for it to appear, then copy the exact same file back into the consume folder. Within a minute the log should show a duplicate notice and the document count should stay unchanged. If a second record appears, stop and capture the log lines before deleting anything.

## Rollback and recovery
If duplicates already exist, delete the newer record from the UI; the original file on disk is untouched. Keep a copy of the consume folder before retesting so you can restore it if a batch import misbehaves. For deeper diagnosis,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult).
