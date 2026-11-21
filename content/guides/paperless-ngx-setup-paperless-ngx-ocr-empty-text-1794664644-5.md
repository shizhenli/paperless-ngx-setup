---
title: "Paperless-ngx OCR Empty Text: When the PDF Imports but Stays Unsearchable"
description: "Troubleshoot Paperless-ngx when a watched PDF imports but its OCR text layer is empty. Check OCR language, image quality, and logs to restore searchable text."
seo_title: "Paperless-ngx OCR Empty Text: Diagnose and Fix Unsearchable PDFs"
seo_description: "Troubleshoot Paperless-ngx when a watched PDF imports but its OCR text layer is empty. Check OCR language, image quality, and logs to restore searchable text."
keywords:
  - "paperless ngx ocr empty text"
date: 2026-11-21T15:20:46+08:00
publishDate: 2026-11-21T15:20:46+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You've dropped a PDF into the watched import volume, Paperless-ngx accepted it, but the document shows no searchable text — a classic **paperless ngx ocr empty text** case.

## Confirm the text layer is truly empty
Open the document in Paperless-ngx and try selecting text in the viewer. If nothing highlights, download the original PDF and run `pdftotext original.pdf -` in a terminal. An empty output confirms the OCR step produced no text. If text appears there, the issue is in Paperless-ngx's indexing, not OCR.

## Check OCR language and image quality
In `docker-compose.yml` or `docker-compose.env`, verify `PAPERLESS_OCR_LANGUAGE` matches the document's language (e.g., `eng` for English). A mismatch often yields empty output. Also inspect the source PDF: if it's a low-resolution scan or a photo with heavy skew, Tesseract may return nothing. Try re-scanning at 300 DPI or preprocessing with a deskew tool.

## Inspect the consume logs
Run `docker logs -f <paperless-container>` and re-import the file. Look for lines mentioning OCR or Tesseract. Errors like "Empty page" or "Image too small" point to the cause. If the log shows OCR completed but no text, the PDF may contain only vector graphics or an image layer Tesseract can't read.

## Verification test
Create a simple text-based PDF (e.g., print "test" to PDF) and import it. If that document is searchable, your OCR pipeline works and the problem is the original file. If it's also empty, the OCR engine or language pack is misconfigured.

## Rollback and recovery
Before changing settings, back up your database and `media` folder. If a configuration change makes things worse, restore the backup and revert the compose file. For persistent cases,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult) with your logs and a sample PDF.

After fixing, re-import the affected document; Paperless-ngx will reprocess it.
