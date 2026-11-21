---
title: "Docker Watched Volume: Why Re-import Creates Duplicates Instead of Deduplicating"
description: "Troubleshoot Paperless-ngx in Docker when re-importing a file from a watched volume creates a duplicate record instead of being deduplicated. Check checksums, watch settings, and run a verification test."
seo_title: "Fix paperless ngx duplicate documents on re-import in Docker"
seo_description: "Troubleshoot Paperless-ngx in Docker when re-importing a file from a watched volume creates a duplicate record instead of being deduplicated. Check checksums, watch settings, and run a verification test."
keywords:
  - "paperless ngx duplicate documents"
date: 2026-11-21T15:20:46+08:00
publishDate: 2026-11-21T15:20:46+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You re-import the same file from your Docker watched volume, expecting Paperless-ngx to skip it, but you end up with two records—so you need to confirm why deduplication is being bypassed before changing anything else.

## Check the document checksums first

Open each duplicate document and compare the stored checksum. In the document details, the checksum should be identical if the file content is truly the same. If the checksums differ, the files are not byte-identical (for example, one was modified by a script), and Paperless-ngx is correctly treating them as separate documents. If the checksums match, deduplication should have blocked the second import, so continue below.

## Confirm the import path and watch settings

In your Docker Compose file, verify the watched volume is mounted to the same container path that Paperless-ngx monitors (commonly /usr/src/paperless/consume). Check the container logs for the consume task and look for a duplicate detection message. If you see the file processed twice with no duplicate warning, the deduplication check is not running against the existing record—often because the second import used a different filename or a subfolder that bypassed the expected path.

## Run a verification test

Create a small test file, import it, then copy the exact same file back into the watched volume. After the consumer runs, list the documents and confirm only one record exists. If a second appears, note the checksum and filename of both records.

## Rollback and recovery

If duplicates already exist, delete the extra record from the Documents list, but keep the original file. Before retrying, move the re-imported file out of the watched volume to avoid a loop. If the behavior persists after these checks,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult) with the checksums and container logs so the **paperless ngx duplicate documents** issue can be diagnosed precisely.
