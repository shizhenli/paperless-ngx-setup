---
title: "Paperless-ngx Duplicate Documents on Re-import: Fix the Watched Volume Deduplication"
description: "Troubleshoot Paperless-ngx duplicate documents when re-importing from a watched volume. Check deduplication settings, file checksums, and verification steps."
seo_title: "Paperless-ngx Duplicate Documents on Re-import: Fix Deduplication"
seo_description: "Troubleshoot Paperless-ngx duplicate documents when re-importing from a watched volume. Check deduplication settings, file checksums, and verification steps."
keywords:
  - "paperless ngx duplicate documents"
date: 2026-11-24T11:04:22+08:00
publishDate: 2026-11-24T11:04:22+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You re-import a file from your watched volume and end up with two records instead of one, so you need to stop the duplicate before it spreads.

## Check the deduplication setting

Open the Paperless-ngx web UI and go to Settings > Import. Confirm that "Enable duplicate detection" is on. If it is off, the same file will always create a new record. Turn it on and save. Then check the consumer logs: run `docker logs <container_name> --tail 100` and look for lines mentioning "duplicate" or "checksum." If you see "Skipping duplicate" for the second import, detection is working; if not, the file content is being altered between imports.

## Verify the file checksum

On the host, run `sha256sum /path/to/watched/file.pdf` before and after the import. If the two hashes differ, something is modifying the file (for example, an OCR step or a sync tool). Paperless-ngx compares checksums, so changed content is treated as a new document. Ensure the watched volume is not being rewritten by another process.

## Verification test

Drop a fresh PDF into the watched volume, wait for consumption, then copy the same file back in. Check the Documents list: you should see one entry, not two. Also confirm the consumer log shows a duplicate skip on the second pass.

## Rollback and recovery

If duplicates already exist, delete the extra record in the UI (the original remains). If you changed import settings, revert them if the test fails. For deeper diagnosis,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult).

If duplicates persist after these checks, the issue is likely **paperless ngx duplicate documents** caused by a checksum mismatch, so re-examine the file source.
