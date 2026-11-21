---
title: "Paperless-ngx OCR Not Working: Fix Empty Text Layer from a Watched Import Volume"
description: "Troubleshoot Paperless-ngx OCR not working when a watched Docker import volume produces empty, unsearchable PDFs. Check OCR language, permissions, and run a verification test."
seo_title: "Paperless-ngx OCR Not Working: Empty Text Layer Fix"
seo_description: "Troubleshoot Paperless-ngx OCR not working when a watched Docker import volume produces empty, unsearchable PDFs. Check OCR language, permissions, and run a verification test."
keywords:
  - "paperless ngx ocr not working"
date: 2026-11-21T15:23:52+08:00
publishDate: 2026-11-21T15:23:52+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You imported a PDF through your Docker watched volume, but the document opens with no selectable or searchable text, so you are stuck at the OCR produces an empty document step.

## Check the OCR language and image quality first
Open the document in Paperless-ngx and confirm the OCR language matches the text in the PDF. A mismatch, such as eng selected for a German scan, often yields an empty layer. Next, check the source file itself: if the scan is very low resolution or nearly blank, OCR has nothing to recognize. Re-scan at 300 DPI and re-import. Also confirm the file is a true PDF and not a renamed image.

## Inspect Docker permissions and the consume path
From the host, run `docker compose exec webserver ls -l /usr/src/paperless/consume` and verify the file appears with read permissions for the paperless user. If permissions are wrong, OCR may be skipped silently. Check the container logs with `docker compose logs webserver` and look for OCR-related errors or language data warnings.

## Verification test
Create a small text PDF, place it in the watched volume, and wait for consumption. Open it and try selecting text. If the text is selectable and searchable, OCR is working. If it remains empty, the issue is in the source file or OCR configuration, not the import path.

## Rollback and recovery
Before changing OCR settings, note your current values. If a change makes things worse, restore the previous configuration and re-import a copy of the original file. Keep the original PDF until you confirm the text layer is correct.

If these checks do not resolve your **paperless ngx ocr not working** issue,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult) with your logs and a sample file.
