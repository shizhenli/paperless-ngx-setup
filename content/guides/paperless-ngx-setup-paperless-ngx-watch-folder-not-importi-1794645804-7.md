---
title: "Paperless-ngx Watch Folder Not Importing: Diagnose the Consume Directory"
description: "Files in your Paperless-ngx watched folder are ignored? Diagnose the consume directory mount, permissions, and polling so imports resume."
seo_title: "Paperless-ngx Watch Folder Not Importing: Fix the Consume Directory"
seo_description: "Files in your Paperless-ngx watched folder are ignored? Diagnose the consume directory mount, permissions, and polling so imports resume."
keywords:
  - "paperless ngx watch folder not importing"
date: 2026-11-15T18:51:33+08:00
publishDate: 2026-11-15T18:51:33+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You placed files in the watched import folder, but Paperless-ngx never picks them up, and you are stuck before running a controlled diagnosis.

## Confirm the container sees the folder

Open a shell in the Paperless container and list the consume path from your configuration, usually /usr/src/paperless/consume. If your files are absent there, the Docker volume is not mounted where Paperless expects. Check the compose file: the host path must map to that exact container path, and the service must be recreated after changes, not just restarted.

## Check permissions and polling

If the files appear in the container but are ignored, compare ownership. The paperless user must be able to read and traverse the directory. Run ls -ln on the folder; a root-owned file with restrictive modes will be skipped. Also confirm PAPERLESS_CONSUMER_POLLING is set if your volume does not deliver filesystem events, since network shares often do not.

## Verification test

Create a small text file directly inside the container consume directory, then watch the logs for a consume event. If that file imports but host-placed files do not, the problem is the mount or permissions, not the consumer. This isolates the failure before you change configuration.

## Rollback note

Before editing compose mounts or environment variables, save a copy of the working file. If imports still fail, restore it and recreate the container to return to the known state.

When the checks above do not resolve why the **paperless ngx watch folder not importing**, gather your compose mount lines and container ls output, then

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult).
