---
title: "Paperless-ngx Duplicate Documents on Re-import: Restore Deduplication"
description: "Troubleshoot Paperless-ngx duplicate documents on re-import in Docker with a watched volume. Check checksums, consumer settings, and verify deduplication."
seo_title: "Paperless-ngx Duplicate Documents: Fix Re-import Duplication"
seo_description: "Troubleshoot Paperless-ngx duplicate documents on re-import in Docker with a watched volume. Check checksums, consumer settings, and verify deduplication."
keywords:
  - "paperless ngx duplicate documents"
date: 2026-11-28T22:16:04+08:00
publishDate: 2026-11-28T22:16:04+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You re-import a file into your Dockerized Paperless-ngx watched volume and end up with two records instead of one, so you need to restore the expected deduplication behavior.

## Check the document checksum

Open the duplicate records in Paperless-ngx and compare their checksums. If the checksums differ, the files are not byte-identical and deduplication will not trigger. If they match, the consumer is likely bypassing the duplicate check.

## Inspect consumer settings

In the Paperless-ngx configuration, confirm that duplicate detection is enabled. Look for the environment variable or config setting that controls duplicate checking and ensure it is not disabled. In Docker, check your compose file or .env for any override that turns off duplicate detection. Also verify the watched volume path is correct and that the consumer is not processing the same file through two different import paths, such as both the consume folder and a mail rule.

## Verify with a controlled test

Create a small test file, place it in the watched volume, and let it import. Then copy the exact same file back into the watched volume and watch the logs. If a second record appears, the duplicate check is not working. If the log shows a duplicate warning, deduplication is active.

## Rollback and recovery

If you changed settings, revert them one at a time and retest. To clean up existing duplicates, delete the extra record manually after confirming which one to keep. This restores the expected outcome where duplicate documents are created on re-import only when the files truly differ. For persistent **paperless ngx duplicate documents**,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult) with your compose file and logs.
