---
title: "Paperless-ngx PDF Not Indexed: Fix Empty OCR Text Layer"
description: "Troubleshoot Paperless-ngx when a PDF imports but its OCR text layer is empty, leaving the document unsearchable. Step-by-step checks and verification."
seo_title: "Paperless-ngx PDF Not Indexed: Fix Empty OCR Text Layer"
seo_description: "Troubleshoot Paperless-ngx when a PDF imports but its OCR text layer is empty, leaving the document unsearchable. Step-by-step checks and verification."
keywords:
  - "paperless ngx pdf not indexed"
date: 2026-11-21T15:23:52+08:00
publishDate: 2026-11-21T15:23:52+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You imported a PDF into Paperless-ngx and the document appears, but searching for its contents returns nothing because the text layer is empty.

## Check the OCR result first
Open the document in Paperless-ngx and look at the content area. If it is blank or only shows a few stray characters, the OCR step produced an empty document. Next, inspect the original PDF outside Paperless-ngx: open it in a PDF viewer and try selecting text. If no text can be selected, the PDF is image-only, which is expected to need OCR. If text is selectable but Paperless-ngx still shows an empty layer, the issue is in the consume pipeline.

## Confirm the OCR language and tooling
In your Docker environment, check the OCR language setting. A mismatch between the document language and the configured OCR language can yield poor or empty output. Also confirm the OCR engine is available inside the container. Run a quick container shell check for the OCR binary and its language data. If the language pack is missing, OCR may silently produce nothing.

## Verify with a controlled test
Place a simple, text-based PDF into the watched import volume and wait for consumption. Then open the new document and search for a word you know is in the file. If the search finds it, the pipeline works and the original PDF is the problem. If the test also fails, the issue is environmental, not document-specific.

## Rollback and recovery
Before changing settings, note your current OCR configuration. If a change makes things worse, restore the previous values and re-consume a copy of the affected PDF. Keep the original file until you confirm the text layer is populated. For persistent cases where the **paperless ngx pdf not indexed** symptom remains after these checks,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult) with your container logs and a sample PDF.
