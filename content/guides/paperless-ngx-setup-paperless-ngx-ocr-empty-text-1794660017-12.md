---
title: "Paperless-ngx OCR Empty Text: When the PDF Imports but the Text Layer Is Blank"
description: "Fix Paperless-ngx when a watched PDF imports but OCR leaves an empty text layer. Check OCR language, image resolution, and run a controlled verification test."
seo_title: "Paperless-ngx OCR Empty Text: Diagnose a Blank Text Layer"
seo_description: "Fix Paperless-ngx when a watched PDF imports but OCR leaves an empty text layer. Check OCR language, image resolution, and run a controlled verification test."
keywords:
  - "paperless ngx ocr empty text"
date: 2026-11-17T11:04:08+08:00
publishDate: 2026-11-17T11:04:08+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

## Troubleshooting

You have a watched import volume in Docker, the PDF appears in Paperless-ngx, but searching it returns nothing because the OCR text layer is empty. Start by confirming whether the file is actually image-only. Open the original PDF in a viewer and try to select text. If nothing highlights, the PDF has no embedded text and OCR is the only path. Next, check the consume log for that document. Run `docker logs <container> 2>&1 | grep -i ocr` and look for lines mentioning tesseract or OCR. A clean run with no errors but an empty result usually points to a missing or mismatched OCR language pack, or an image too low-resolution for tesseract to read. Confirm the language setting in your Docker environment, for example `PAPERLESS_OCR_LANGUAGE=eng`, and verify the corresponding tesseract data is present in the container. If the PDF is a scan at very low DPI or heavily skewed, OCR may return whitespace. As a controlled test, copy one suspect PDF into a separate folder, run `pdftoppm -r 300 -png input.pdf page` to rasterize it, then OCR a page with `tesseract page-1.png stdout`. If stdout is empty, the source image is the problem; if it contains text, the Paperless-ngx pipeline configuration is the problem. This is the practical **paperless ngx ocr empty text** diagnosis. After adjusting language or resolution, re-import a copy and search for a known word. If results are still blank, roll back by restoring the previous OCR language setting and re-running the consume step on the original file so you do not lose the document. For a guided review of your Docker OCR configuration,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult).
