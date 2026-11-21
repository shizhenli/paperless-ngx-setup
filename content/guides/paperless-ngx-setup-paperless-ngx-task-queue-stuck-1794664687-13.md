---
title: "Paperless-ngx Task Queue Stuck: Diagnose a File That Never Leaves Consume"
description: "A file remains in the consume queue and never becomes searchable. Follow these checks to diagnose a paperless ngx task queue stuck in Docker and restore normal processing."
seo_title: "Paperless-ngx Task Queue Stuck: Fix a File Stuck in Consume"
seo_description: "A file remains in the consume queue and never becomes searchable. Follow these checks to diagnose a paperless ngx task queue stuck in Docker and restore normal processing."
keywords:
  - "paperless ngx task queue stuck"
date: 2026-11-21T15:23:52+08:00
publishDate: 2026-11-21T15:23:52+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

## Check the task worker and queue state

You have a file sitting in the consume queue in Docker, and it never turns into a searchable document. First, confirm the worker is alive: run `docker compose ps` and look at the `webserver` and `broker` (Redis) containers. If either is stopped or restarting, that is your cause. Next, open the task list in the web UI and note the task ID. A task stuck at `PENDING` means the worker never picked it up; a task at `STARTED` that never advances points to a blocked consumer.

## Inspect the consume directory and permissions

Enter the webserver container with `docker compose exec webserver bash`. List `/usr/src/paperless/consume` and check that the file is present and not zero bytes. Run `ls -l` and confirm the `paperless` user owns the file. If it is owned by root, the consumer cannot read it and the task will hang. Also check `docker compose logs webserver` for repeated errors such as `Permission denied` or `database is locked`; those exact lines tell you whether this is a file access or database contention problem.

## Verification test

Place a small, known-good PDF into the consume volume and watch the task list. If the new file processes and becomes searchable, the original file is corrupt or wrongly owned. If the new file also stalls, the worker or broker is the fault, not the file.

## Recovery and rollback

Before changing anything, copy the stuck file out of the consume volume. If you restarted services and the queue still will not clear, stop the stack with `docker compose down`, restart it, and re-add the file. Keep the original copy until the document appears in search. If the queue remains stuck after these checks,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult) with the task ID and the relevant log lines.

A **paperless ngx task queue stuck** at consume is usually a worker, ownership, or broker issue, and the checks above isolate which one.
