---
title: Paperless-ngx Setup Help
seo_title: Paperless-ngx OCR and Import Help
seo_description: Remote Paperless-ngx help for documents stuck in consume, OCR failures, and search verification.
keywords:
- paperless ngx ocr not working
- paperless ngx import stuck
- paperless consume folder
hero_kicker: DOCUMENT WORKFLOW
hero_h1: Paperless-ngx won't import or OCR?
hero_lead: Follow one sample document from the consume folder through OCR and search, then verify the stored file and metadata.
hero_proof:
- One sample document through the full path
- OCR text checked, not just a green status
- Original file and metadata retained
hero_alt: A document scanner beside a laptop running a Paperless-ngx archive
hero_title: Paperless-ngx document import and OCR
body_alt: A scanned document and Paperless-ngx search result on a laptop
body_title: Paperless-ngx OCR and search verification
body_caption: A successful import has a stored original, readable OCR text, and a search result.
failures_kicker: THE THREE FAILURES WE SEE
failures_heading: What is going wrong?
test_kicker: HOW WE TEST
test_heading: Find the failing layer, then prove the fix.
scope_kicker: SERVICE SCOPE
scope_heading: What is included
scope_note: The session covers one agreed device and one verified path. Anything outside the written scope is quoted separately before work starts.
faq_kicker: FAQ
faq_heading: Before you book
consult_heading: Tell us what is happening.
consult_lead: Send the device, software, exact symptom, and what you have already tried. We will confirm scope and a fixed quote before booking.
form_placeholder: Device, software version, exact symptom, and what you have tried.
first_guide_url: /guides/paperless-ngx-setup/
symptoms:
- title: The file never leaves consume
  text: A PDF or image sits in the consume folder while the dashboard shows no new document.
- title: OCR text is empty or wrong
  text: The document appears, but search cannot find its text or the OCR layer is garbled.
- title: Search cannot find the document
  text: The correct correspondent or tag exists, but a known phrase returns no result.
checks:
- title: Confirm the consume path
  text: Check the container's consume mount and permissions. A file in the host folder is not necessarily visible inside the container.
- title: Read the task and worker logs
  text: Find the document task and its failure message. Separate a file/permission error from an OCR language or dependency error.
- title: Test one clean document
  text: Use a simple, text-based PDF or a clear scan, then wait for the task to finish before uploading a batch.
- title: Verify original, OCR, and search
  text: Open the stored original, inspect the text layer, and search for a unique phrase from the page.
included:
- One Paperless-ngx instance and one sample document
- Consume-path and permission check
- OCR/task log review
- Original, text, and search verification
excluded:
- Scanning a whole physical archive
- Repairing a damaged database
- Custom OCR model training
- Guaranteeing every handwriting style
faqs:
- q: Will you delete my documents?
  a: No. The test uses one sample and leaves existing documents untouched.
- q: Why does OCR fail on a clear scan?
  a: The container may lack the language pack, the file may be image-only, or the task may have stopped before OCR. The task log distinguishes these.
- q: Can you set up automatic tagging?
  a: Basic matching rules can be reviewed, but this session focuses on a working import, OCR, and search path.
---
