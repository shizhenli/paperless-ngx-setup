---
title: "Paperless-ngx PDF Not Indexed: When OCR Produces an Empty Document"
description: "Troubleshoot Paperless-ngx when a PDF imports but its text layer is empty, so OCR produces an empty document and the file stays unsearchable."
seo_title: "Paperless-ngx PDF Not Indexed: Fix Empty OCR Text Layer"
seo_description: "Troubleshoot Paperless-ngx when a PDF imports but its text layer is empty, so OCR produces an empty document and the file stays unsearchable."
keywords:
  - "paperless ngx pdf not indexed"
date: 2026-11-25T11:04:14+08:00
publishDate: 2026-11-25T11:04:14+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You imported a PDF into Paperless-ngx, but the document shows no searchable text, and you are stuck at the OCR produces an empty document step.

## Confirm the PDF actually has a text layer
Open the original PDF on your desktop and try selecting text. If nothing highlights, the file is image-only and OCR must run. In Paperless-ngx, open the document and check the content panel. An empty panel means the consumer finished without extracting text.

## Check the consume logs for OCR errors
Run `docker logs --tail 200 <consumer-container>` and look for lines containing `OCR` or `ocrmypdf`. A message like `OCRmyPDF exited with code 1` or `unpaper` errors means OCR failed before writing text. If you see `Skipping OCR, using existing text`, the PDF already had a text layer, but it may be blank or corrupt.

## Verify the watched volume and file permissions
Confirm the PDF landed in the consume directory and that the container user can read it. Run `docker exec <consumer-container> ls -l /usr/src/paperless/consume`. If the file is owned by root with mode 600, the consumer cannot read it. Fix ownership with `chown 1000:1000` on the host, then re-import.

## Test with a known-good PDF
Create a simple text PDF, drop it into the consume folder, and wait for the consumer to finish. If that document indexes correctly, the problem is the original PDF, not Paperless-ngx. If it also stays empty, the OCR pipeline itself is broken. This is the point where many users search for **paperless ngx pdf not indexed** fixes and miss the container-level cause.

## Rollback and recovery
Before changing permissions or reinstalling OCR tools, back up the database and the consume folder. If a fix makes things worse, restore the backup and remove the failed document from the queue with `docker compose restart webserver consumer`.

If the logs still show OCR failures after these checks,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult) with the exact error lines and your compose file.
