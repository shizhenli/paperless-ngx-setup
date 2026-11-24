---
title: "Paperless-ngx OCR Empty Text Layer: Quick Fix for Docker Watched Import"
description: "Troubleshoot Paperless-ngx OCR producing empty documents in Docker. Check OCR language, permissions, and run a verification test to restore searchable text."
seo_title: "Paperless-ngx OCR Not Working: Fix Empty Text Layer in Docker"
seo_description: "Troubleshoot Paperless-ngx OCR producing empty documents in Docker. Check OCR language, permissions, and run a verification test to restore searchable text."
keywords:
  - "paperless ngx ocr not working"
date: 2026-11-24T09:04:16+08:00
publishDate: 2026-11-24T09:04:16+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You've set up Paperless-ngx in Docker with a watched import volume, but when a PDF is imported, its text layer is empty or unsearchable—meaning OCR isn't working as expected.

## Check OCR Language and Permissions

First, confirm the OCR language is installed. Inside the container, run `tesseract --list-langs`. You should see your configured language (e.g., `eng`). If it's missing, install the appropriate language pack in your Dockerfile or container. Next, verify the watched volume permissions: the `paperless` user (UID 1000 by default) must have read access to incoming files. Run `docker exec -it <container> ls -l /usr/src/paperless/consume` and ensure files are readable. If permissions are wrong, adjust them on the host with `chown -R 1000:1000 /path/to/consume`.

## Inspect the Consume Logs

Check the container logs for OCR errors: `docker logs <container> 2>&1 | grep -i ocr`. Look for messages like "OCR failed" or "no text". If you see permission denied or missing language errors, fix those first. This is a common reason for **paperless ngx ocr not working**.

## Verification Test

Create a test PDF with a clear text layer using `echo "Test OCR 123" | ps2pdf - test.pdf` (or use a scanned image). Place it in the consume volume. After import, open the document in Paperless-ngx and check if the text is searchable. Alternatively, run `pdftotext` on the archived PDF to confirm text extraction.

## Rollback and Recovery

If changes cause issues, revert permission or language modifications. You can re-run the consumer with `docker compose restart webserver` to reprocess failed documents. For persistent problems, consider

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult) from our team.

By methodically checking language, permissions, and logs, you can restore OCR functionality and ensure searchable documents.
