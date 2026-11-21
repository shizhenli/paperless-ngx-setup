---
title: "Paperless-ngx OCR Not Working: Empty Text Layer in Docker Watched Import"
description: "Paperless-ngx OCR not working in Docker with a watched import volume? Diagnose empty text layers with targeted checks, a verification test, and rollback steps."
seo_title: "Paperless-ngx OCR Not Working: Fix Empty Text Layer (Docker Watched Import)"
seo_description: "Paperless-ngx OCR not working in Docker with a watched import volume? Diagnose empty text layers with targeted checks, a verification test, and rollback steps."
keywords:
  - "paperless ngx ocr not working"
date: 2026-11-21T15:17:41+08:00
publishDate: 2026-11-21T15:17:41+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

**paperless ngx ocr not working**. You imported a PDF through a watched Docker volume, but the document opens with no selectable text and search returns nothing—so you are stuck at the OCR produces an empty document step. Let’s isolate whether the issue is the source file, the OCR language data, or the consumer container before changing anything.

## Check the source PDF and OCR language data

First, confirm the PDF actually contains a scanned image rather than a broken or blank page. Open it locally and try selecting text; if nothing highlights, OCR is required. Next, inspect the consumer logs for OCR-related errors:

```bash
docker compose logs consumer | grep -i ocr
```

Look for messages about missing Tesseract language files or unsupported image formats. If you see language errors, verify that your configured OCR language (for example, `eng`) is installed in the container. A missing language pack will let the PDF import but produce an empty text layer.

## Verify the watched volume and consumer restart

Confirm the watched import folder is mounted read-write and that the consumer container can read the file. Then restart only the consumer service and re-import a small test scan:

```bash
docker compose restart consumer
```

After the test document finishes consuming, open it and try selecting text. If the text layer is still empty, run OCR manually inside the container on a copy of the PDF to see the exact error. This separates a Paperless-ngx configuration problem from a Tesseract problem. If the manual run succeeds but the import still fails, the issue is in the consumer configuration or volume permissions—not OCR itself.

## Rollback and recovery

Before changing OCR settings, note your current `PAPERLESS_OCR_LANGUAGE` value and keep a copy of the original PDF. If a configuration change makes things worse, restore the previous value and re-consume the document from the original file. For persistent cases,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult) with the consumer log excerpt and the manual OCR result.

A controlled diagnosis here prevents repeated empty imports and restores searchable text.
