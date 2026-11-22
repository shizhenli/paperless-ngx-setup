---
title: "Paperless-ngx OCR Empty Text: Diagnose the Text Layer Before Re-importing"
description: "A practical troubleshooting guide for Paperless-ngx in Docker when OCR produces an empty text layer. Check OCR language, image quality, and consumer logs to restore searchable PDFs."
seo_title: "Paperless-ngx OCR Empty Text: Fix Unsearchable PDFs in Docker"
seo_description: "A practical troubleshooting guide for Paperless-ngx in Docker when OCR produces an empty text layer. Check OCR language, image quality, and consumer logs to restore searchable PDFs."
keywords:
  - "paperless ngx ocr empty text"
date: 2026-11-22T12:04:35+08:00
publishDate: 2026-11-22T12:04:35+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You have a PDF sitting in your watched import volume, Paperless-ngx imports it, but the document text is empty and searches return nothing.

## Confirm the text layer is actually missing

Open the document in Paperless-ngx and try selecting text in the viewer. If nothing highlights, the text layer is empty. Next, check the consumer logs:

`docker logs --tail 200 <consumer_container_name>`

Look for lines mentioning OCR, Tesseract, or the document filename. A successful OCR run usually logs page counts and completion. If you see no OCR lines at all, the file may have been treated as a native PDF with no text, which still produces an empty result.

## Check OCR language and image quality

In `docker-compose.yml`, verify `PAPERLESS_OCR_LANGUAGE` matches the document language, for example `eng` for English. A wrong or missing language pack causes Tesseract to return empty output. Also confirm the PDF pages contain visible, reasonably sharp text; blurry scans or very low resolution can defeat OCR. If the PDF is a photo with heavy skew, rotate or re-scan it before import.

## Run a controlled verification test

Create a simple one-page PDF with clear black text on white, place it in the watched volume, and wait for consumption. Then open it and try selecting the text. If that test document becomes searchable, the pipeline is healthy and the original file is the problem. If the test also produces an empty layer, the issue is configuration or the container image.

## Rollback and recovery

Before changing compose settings, back up the database and the `media` directory. If a change makes things worse, restore those backups and revert the compose file. Re-importing the same file after a fix is safe once you confirm deduplication is not blocking it.

If these checks leave you with **paperless ngx ocr empty text** and no clear cause,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult) with your compose file and consumer logs.
