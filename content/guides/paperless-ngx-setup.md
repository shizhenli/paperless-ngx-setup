---
title: 'Paperless-ngx Import or OCR Fails: Read the Consume Path and Task Log'
description: A single-document path through Paperless-ngx that checks consume visibility, task logs, OCR text, and search.
seo_title: Paperless-ngx Import Stuck or OCR Missing?
seo_description: A single-document path through Paperless-ngx that checks consume visibility, task logs, OCR text, and search.
keywords:
- paperless ngx ocr not working
- paperless ngx import stuck
- paperless consume folder
date: '2026-11-13'
draft: false
categories:
- Document Workflow
category_slug: document-workflow
---

<p>Paperless-ngx problems split into file visibility, task processing, and OCR quality. Test one document so each layer has a clear result.</p>
<h2>Before you start</h2><ul>
<li>Paperless-ngx URL and admin/user access</li>
<li>The host consume path and container mount</li>
<li>One simple PDF or clear scan</li>
<li>Access to task/worker logs</li>
</ul>
<h2>Step-by-step checks</h2>
<h3>Step 1: Verify the consume mount</h3>
<p>Compare the host folder with the container's consume path. Create a tiny text PDF in the host folder and confirm it appears inside the container with readable permissions.</p>
<p><strong>Check:</strong> The same file is visible at the configured consume path in the container.</p>
<h3>Step 2: Watch one task</h3>
<p>Upload the sample and follow its task in the UI and worker log. Record the document ID, start time, and first error rather than re-uploading repeatedly.</p>
<p><strong>Check:</strong> The task reaches success or gives a specific failure message.</p>
<h3>Step 3: Check OCR text</h3>
<p>Open the document's text panel and compare a unique phrase from the page. If the text is empty, confirm the OCR language and whether the PDF already contains a text layer.</p>
<p><strong>Check:</strong> A unique phrase from the page appears in the text panel.</p>
<h3>Step 4: Search and open the original</h3>
<p>Search for the unique phrase and open the result. Download the stored original and confirm it matches the source file.</p>
<p><strong>Check:</strong> Search returns the document and the original opens correctly.</p>
<h3>Step 5: Repeat with one real sample</h3>
<p>Once the simple file passes, repeat with one representative real document. Note any difference in scan quality, language, or file size.</p>
<p><strong>Check:</strong> The real sample also imports, OCRs, and searches.</p>
<h2>Verify the result</h2>
<p>The sample has a stored original, readable OCR text, and a search hit. The task log shows no hidden failure after the document appears.</p>
<h2>Rollback</h2>
<p>Remove only the test document and its test tag after verification. Keep the original scan outside Paperless-ngx.</p>
<p><a href="/#consult">Ask for help with this exact symptom</a> · <a href="/guides/">All troubleshooting guides</a></p>
