---
title: "Paperless-ngx PDF Not Indexed: Fix an Empty OCR Text Layer"
description: "Troubleshoot Paperless-ngx when a PDF imports but its OCR text layer is empty, so the document is unsearchable. Check OCR language, image quality, and consumer logs."
seo_title: "Paperless-ngx PDF Not Indexed: Empty OCR Text Layer Fix"
seo_description: "Troubleshoot Paperless-ngx when a PDF imports but its OCR text layer is empty, so the document is unsearchable. Check OCR language, image quality, and consumer logs."
keywords:
  - "paperless ngx pdf not indexed"
date: 2026-11-16T08:04:05+08:00
publishDate: 2026-11-16T08:04:05+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You imported a PDF into Paperless-ngx from your Docker watched volume, but the document opens with no searchable text and the OCR step seems to produce an empty result.

## Confirm the text layer is truly empty

Open the document in Paperless-ngx and try selecting text in the viewer. If nothing highlights, the PDF has no embedded text layer. Next, check the consumer logs for that document:

```
docker compose logs --tail=100 webserver
```

Look for OCR lines mentioning the file. If you see "OCR failed" or a language error, note the exact message. If OCR ran without errors but the text is still empty, the source images are likely too low-contrast or rotated.

## Check OCR language and image quality

In your Docker environment file, confirm `PAPERLESS_OCR_LANGUAGE` matches the document's language (for example, `eng`). A mismatch can cause OCR to return nothing. Then open the PDF in a viewer and zoom in. If the scan is skewed, faint, or below roughly 200 DPI, OCR may produce no usable characters. Re-scan at 300 DPI or run a preprocessing step before import.

## Verification test

Place a known-good text PDF into the watched volume and wait for consumption. Search for a word you know is in that file. If it appears in results, your pipeline is working and the original PDF is the problem. If it does not, the issue is environmental—check volume permissions and the consumer container.

## Rollback and recovery

If a configuration change caused the failure, revert `PAPERLESS_OCR_LANGUAGE` to its previous value and restart the containers with `docker compose up -d`. Re-import the affected PDF after deleting the failed document, so the consumer processes it fresh.

If the **paperless ngx pdf not indexed** symptom persists after these checks,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult) with your logs and a sample page.
