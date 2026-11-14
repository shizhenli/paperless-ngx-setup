---
title: "Paperless-ngx OCR Not Working: Empty Text Layer After Import"
description: "Troubleshoot Paperless-ngx OCR not working when PDFs import with an empty or unsearchable text layer from a Docker watched volume. Step-by-step checks and a verification test."
seo_title: "Paperless-ngx OCR Not Working: Fix Empty Text Layer"
seo_description: "Troubleshoot Paperless-ngx OCR not working when PDFs import with an empty or unsearchable text layer from a Docker watched volume. Step-by-step checks and a verification test."
keywords:
  - "paperless ngx ocr not working"
date: 2026-11-14T19:32:27+08:00
publishDate: 2026-11-14T19:32:27+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You imported a PDF through your Docker watched volume, but the document opens with no selectable text and searches return nothing, so you are stuck at the ocr produces an empty document step.

## Confirm the file actually needs OCR

First, check whether the PDF already has a text layer. Run `pdftotext -layout file.pdf - | head` on the host. If it prints readable words, OCR is not the problem; the import or consumption stage is. If it prints nothing, continue.

## Check the OCR language and image quality

Inside the container, run `tesseract --list-langs`. If your document's language is missing, install the matching language pack in your Dockerfile or image. Then inspect the scan: a low-resolution or skewed image can make OCR return empty output. Try `pdftoppm -r 300 -png file.pdf page` and run `tesseract page-1.png stdout` to see whether text appears at 300 DPI.

## Verify the watched volume and permissions

The consumer must read the file. Check `docker logs paperless-ngx` for permission errors or skipped files. Confirm the watched folder is mounted read-write and that the container user can read the PDF. If the file is moved before consumption finishes, OCR may run on a partial file.

## Verification test

Place a single clean, 300 DPI scanned PDF into the watched volume. Wait for the consumer to finish, then open the document and select text. A successful result shows selectable words and search hits. If the text layer is still empty, the issue is in OCR configuration, not the import path.

## Rollback and recovery

Before changing language packs or image settings, copy your compose file and note the current image tag. If a change breaks consumption, restore the previous compose file and restart the stack. Re-upload the test PDF to confirm recovery.

If these checks do not resolve your **paperless ngx ocr not working** case,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult) with your logs and test PDF details.
