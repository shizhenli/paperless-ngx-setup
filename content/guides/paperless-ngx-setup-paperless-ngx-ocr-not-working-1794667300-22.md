---
title: "Paperless-ngx OCR Not Working: Empty Text Layer from a Watched Docker Volume"
description: "Troubleshoot Paperless-ngx OCR not working when a PDF from a Docker watched volume imports with an empty text layer. Step-by-step checks and a verification test."
seo_title: "Paperless-ngx OCR Not Working: Fix Empty Text Layer in Docker"
seo_description: "Troubleshoot Paperless-ngx OCR not working when a PDF from a Docker watched volume imports with an empty text layer. Step-by-step checks and a verification test."
keywords:
  - "paperless ngx ocr not working"
date: 2026-11-26T10:03:23+08:00
publishDate: 2026-11-26T10:03:23+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You import a scanned PDF into the watched volume, but the document arrives with an empty, unsearchable text layer and OCR seems to have done nothing. This is a common **paperless ngx ocr not working** symptom, and you can isolate it before changing any settings.

## Check the OCR language and engine

Open your Docker compose file and confirm the OCR language pack matches the document language. A missing or mismatched language pack can make OCR return empty text. Also check that the OCR engine is enabled and not disabled by an environment variable. After any change, recreate the container rather than just restarting it.

## Inspect the consume logs for OCR errors

Run `docker logs <container> --tail 200` and look for lines mentioning OCR, Tesseract, or language data. If you see a missing language file error, install that pack. If OCR is skipped entirely, the document may already contain a text layer that is empty, so Paperless-ngx trusts it and does not re-OCR.

## Test with a known-good scan

Drop a simple, high-contrast scanned page into the watched volume and wait for import. Open the document and try selecting text. If the new file is searchable, the original PDF was the problem; if it is also empty, the container configuration is at fault. This verification test separates document issues from setup issues.

## Rollback and recovery

Before editing compose settings, copy your current file. If a change makes imports worse, restore the backup and recreate the container. You can also re-run consumption on the affected document after fixing the cause.

If these checks do not restore OCR,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult) with your compose file and logs.
