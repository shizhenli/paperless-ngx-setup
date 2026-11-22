---
title: "Paperless-ngx Duplicate Documents After Re-import: Why Deduplication Misses and How to Fix It"
description: "Troubleshoot Paperless-ngx duplicate documents created when the same file is re-imported into a Docker watched volume. Check checksum settings, verify deduplication, and recover safely."
seo_title: "Paperless-ngx Duplicate Documents on Re-import: Fix Deduplication"
seo_description: "Troubleshoot Paperless-ngx duplicate documents created when the same file is re-imported into a Docker watched volume. Check checksum settings, verify deduplication, and recover safely."
keywords:
  - "paperless ngx duplicate documents"
date: 2026-11-22T11:04:23+08:00
publishDate: 2026-11-22T11:04:23+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You re-import a file you know is already in Paperless-ngx, and instead of being rejected as a duplicate, a second record appears. This is a common issue when the watched import volume is used, and it usually points to a mismatch in how deduplication is evaluated.

## Check the checksum and duplicate detection settings

Open the Paperless-ngx admin interface and go to the configuration for duplicate detection. Confirm that duplicate checking is enabled and that the checksum algorithm matches what was used when the original document was imported. If the original was imported before a configuration change, its stored checksum may not match the new algorithm, so the re-import is treated as new. Also verify that the file is not being modified between imports (for example, by a sync tool or metadata write), because any byte change produces a different checksum.

## Confirm the watched volume behavior

Inspect the Docker volume mapped to the consume directory. If files are copied in with altered timestamps or line endings, the checksum changes. Use `sha256sum` on the original and the re-imported copy; if the hashes differ, deduplication cannot work. This is the root cause of most **paperless ngx duplicate documents** cases.

## Verification test

Import a known file, note its checksum, then re-import the exact same bytes. The second import should be skipped or flagged. If a second record appears, recheck the checksum comparison and the duplicate settings.

## Rollback and recovery

If duplicates already exist, delete the extra records from the admin interface after confirming which is the original. Keep a backup of the database before bulk cleanup. For guided diagnosis,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult).
