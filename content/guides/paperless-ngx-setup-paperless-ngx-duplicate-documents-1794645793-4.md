---
title: "Paperless-ngx Duplicate Documents on Re-import: Fix the Deduplication Check"
description: "Troubleshoot Paperless-ngx duplicate documents when the same file in a watched Docker volume imports twice. Check checksums, consumer logs, and task settings, then verify with a test file."
seo_title: "Paperless-ngx Duplicate Documents: Fix Re-import Deduplication"
seo_description: "Troubleshoot Paperless-ngx duplicate documents when the same file in a watched Docker volume imports twice. Check checksums, consumer logs, and task settings, then verify with a test file."
keywords:
  - "paperless ngx duplicate documents"
date: 2026-11-14T19:32:27+08:00
publishDate: 2026-11-14T19:32:27+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You re-drop the same file into your watched import volume and Paperless-ngx creates a second record instead of recognizing it as a duplicate.

## Confirm the duplicate is a true re-import

Open both documents and compare the original filename, checksum, and created timestamp. If the checksums match but the IDs differ, the consumer processed the file twice rather than deduplicating it. If the checksums differ, the files are not identical and deduplication is working as designed.

## Check the consumer log for the second pass

Run `docker compose logs -f webserver` and watch for the import line for your test file. A healthy re-import logs a duplicate warning and skips creation. If you see a full consumption sequence again, the file was moved or copied into the volume in a way that reset its identity, or the consumer was restarted mid-task and retried.

## Inspect the watched volume behavior

Confirm your volume mount is not syncing from a source that rewrites files, such as a cloud client that changes timestamps. Also check that `PAPERLESS_CONSUMER_DELETE_DUPLICATES` is set to true in your environment file and that the container was recreated after the change, not just restarted.

## Verification test

Create a small text file, let it import, then copy it back into the watched folder with the same name. The log should show a duplicate notice and the document count should stay the same. If a new record appears, the setting is not active.

## Recovery note

If duplicates already exist, delete the extra records from the UI after confirming which one holds your tags and correspondences. Keep a database backup before bulk cleanup. For a guided review of your compose configuration,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult).

This isolates whether **paperless ngx duplicate documents** come from configuration or from the volume itself.
