---
title: "Paperless-ngx OCR Empty Text: Diagnose the Text Layer in Docker"
description: "Diagnose why Paperless-ngx OCR produces an empty text layer for PDFs in Docker, with checks, verification, and recovery steps."
seo_title: "Paperless-ngx OCR Empty Text: Fix Empty Text Layer in Docker"
seo_description: "Diagnose why Paperless-ngx OCR produces an empty text layer for PDFs in Docker, with checks, verification, and recovery steps."
keywords:
  - "paperless ngx ocr empty text"
date: 2026-11-24T12:05:55+08:00
publishDate: 2026-11-24T12:05:55+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You've imported a PDF into Paperless-ngx Docker, but the document shows no searchable text, and you're stuck at the ocr produces an empty document step before a controlled diagnosis.

## Check the OCR Language and Engine

First, confirm the OCR language is installed in the container. Run `docker exec -it paperless-ngx ocrmypdf --version` and then check available Tesseract languages with `docker exec -it paperless-ngx tesseract --list-langs`. If your document's language is missing, OCR may yield empty text. Set `PAPERLESS_OCR_LANGUAGE` to an installed language and restart the container. Also verify `PAPERLESS_OCR_MODE` is not set to `skip` or `redo` in a way that bypasses text extraction.

## Inspect the Original PDF

Open the source PDF outside Paperless-ngx. If it is a pure image scan, OCR should add text. If it already has a text layer, Paperless-ngx may skip OCR. Check the consume log with `docker logs paperless-ngx` for lines mentioning OCR or empty output. A message like "no text found" indicates the OCR engine ran but produced nothing, often due to low resolution or unusual fonts.

## Verification Test

Create a simple test: place a clear, high-contrast scanned PDF into the watched volume. After import, open the document in Paperless-ngx and use the search box for a word you know appears. If it returns the document, OCR is working. If not, run `docker exec -it paperless-ngx ocrmypdf --sidecar - - < /path/to/test.pdf` to see raw OCR output.

## Rollback and Recovery

If changes to environment variables worsen behavior, revert them and restart the container. For documents already imported with empty text, delete and re-import after fixing the configuration. If the issue persists,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult) with your logs and a sample PDF.

This targeted check should resolve most **paperless ngx ocr empty text** cases.
