---
title: "Paperless-ngx PDF Not Indexed: Fix the Empty Text Layer"
description: "Troubleshoot a Paperless-ngx PDF that imports but stays unsearchable because OCR produces an empty document. Check image quality, OCR language, and verify the text layer."
seo_title: "Paperless-ngx PDF Not Indexed: Restore OCR Text Layer"
seo_description: "Troubleshoot a Paperless-ngx PDF that imports but stays unsearchable because OCR produces an empty document. Check image quality, OCR language, and verify the text layer."
keywords:
  - "paperless ngx pdf not indexed"
date: 2026-11-23T11:04:22+08:00
publishDate: 2026-11-23T11:04:22+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You imported a PDF into Paperless-ngx and it appears in the document list, but searching for known words returns nothing because the text layer is empty.

## Confirm the text layer is actually empty
Open the document in Paperless-ngx and select the text view. If you see only whitespace or a single blank page, the OCR step produced no characters. Next, check the original file on your watched import volume: open it in a PDF viewer and try to select text. If the selection is empty, the PDF is image-only and OCR must do the work.

## Check OCR settings and image quality
In the Paperless-ngx admin, open the OCR settings and confirm the language matches the document. A mismatch (for example, English selected for a German scan) often yields an empty result. Also inspect the scan resolution: text below roughly 200 DPI is frequently missed. If the PDF is a photo with low contrast or heavy skew, OCR may return nothing even though the file imports cleanly.

## Run a controlled verification test
Place a simple, high-contrast text PDF into the watched volume and let it consume. Open the new document and check the text view. If that test document shows readable text, your pipeline is working and the original PDF is the problem. If the test also stays empty, the OCR service or language data is misconfigured.

## Rollback and recovery
Before changing settings, note the current OCR language and any custom parameters. If a configuration change makes things worse, restore those values and re-run the original file through the consumer. Deleting and re-importing the document is safe once you have confirmed the source file is intact.

When the text layer remains empty after these checks, this is a classic case of **paperless ngx pdf not indexed**, and a focused review of the OCR logs will show the exact failure.

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult) if you want a technician to read those logs with you.
