---
title: "Paperless-ngx OCR Produces an Empty Document: Diagnose the Text Layer"
description: "Troubleshoot Paperless-ngx when OCR produces an empty document after import from a watched Docker volume, with checks, result interpretation, and a verification test."
seo_title: "Paperless-ngx OCR Empty Text: Fix an Empty Text Layer"
seo_description: "Troubleshoot Paperless-ngx when OCR produces an empty document after import from a watched Docker volume, with checks, result interpretation, and a verification test."
keywords:
  - "paperless ngx ocr empty text"
date: 2026-11-15T18:51:33+08:00
publishDate: 2026-11-15T18:51:33+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You imported a PDF from your watched volume, but the document opens with no selectable text and searches return nothing, so you need to diagnose why **paperless ngx ocr empty text** is happening before changing settings.

## Confirm the source file actually has a text layer

Run `pdftotext -layout /path/to/imported.pdf -` on the original file inside the container or on the host. If this prints readable text, the source is text-based and OCR is not the problem; if it prints nothing, the PDF is image-only and OCR must supply the text. Next, check the Paperless-ngx task log for the consume job and look for OCRmyPDF messages such as "no text found" or "skipping OCR". A completed job with no OCR lines usually means the text layer was considered present or the OCR step was skipped.

## Interpret the result

If the source has no text and the task log shows OCR skipped, verify that OCR is enabled in your configuration and that the language pack for the document is installed. If the source has text but Paperless-ngx shows none, the PDF may use an encoding the parser cannot read; re-export it as a standard PDF and re-import through the watched volume. If OCR runs but produces garbage, the scan resolution is likely too low; aim for 300 DPI.

## Verification test

Place one known scanned PDF into the watched volume, wait for the consume task to finish, then open the document and try selecting a sentence. A successful fix gives selectable text and search hits for a word you can see on the page.

## Rollback note

If a configuration change makes imports worse, revert the OCR-related environment variables to their previous values, restart the container, and re-import a copy of the test PDF rather than the original.

If the log still shows no OCR activity after these checks,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult) with the task log and a sample page.
