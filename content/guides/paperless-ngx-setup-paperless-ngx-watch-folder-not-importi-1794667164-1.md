---
title: "Paperless-ngx Watch Folder Not Importing: A Controlled Diagnosis for Docker Volumes"
description: "Files in your watched folder are ignored by Paperless-ngx in Docker? Follow these checks to diagnose why the watch folder is not detected and restore imports."
seo_title: "Paperless-ngx Watch Folder Not Importing: Docker Volume Diagnosis"
seo_description: "Files in your watched folder are ignored by Paperless-ngx in Docker? Follow these checks to diagnose why the watch folder is not detected and restore imports."
keywords:
  - "paperless ngx watch folder not importing"
date: 2026-11-23T09:04:11+08:00
publishDate: 2026-11-23T09:04:11+08:00
draft: false
categories:
  - "Document Workflow"
category_slug: "document-workflow"
---

You placed files in the watched import folder, but Paperless-ngx in Docker never picks them up, and you are stuck before completing a controlled diagnosis. Let's walk through this step by step.

## Confirm the container sees the folder

First, check that the watched folder is mounted correctly inside the container. Run `docker exec -it <container> ls -la /usr/src/paperless/consume` (adjust the path to your actual consume directory). If you see your files, the mount is working. If the directory is empty or missing, the volume mapping in your Docker Compose file is wrong. Verify the host path and container path match your configuration.

## Check file permissions

The container user (usually UID 1000) must be able to read files in the watched folder. Run `docker exec -it <container> stat /usr/src/paperless/consume/yourfile.pdf`. Look at the owner and permissions. If the file is owned by root with mode 600, the container cannot read it. Fix by setting ownership to the container user on the host: `sudo chown -R 1000:1000 /path/to/consume`.

## Verify the consumer is running

A common cause of **paperless ngx watch folder not importing** is that the consumer service is not active. Check logs with `docker logs <container> | grep consume`. You should see polling messages. If the consumer is disabled or crashed, restart the container and watch for errors.

## Run a verification test

Create a simple text file in the watched folder: `echo "test" > /path/to/consume/test.txt`. Wait up to 60 seconds (default polling interval). Then check the Paperless-ngx dashboard for a new document. If it appears, the pipeline works. If not, review the consumer logs for permission or path errors.

## Rollback and recovery

If changes make things worse, revert your Docker Compose volume mappings and permission changes to the last known working state. Keep a backup of your compose file before editing.

If the folder still is not detected after these checks,

![A scanned document and Paperless-ngx search result on a laptop](/images/service-detail-960.webp "Paperless-ngx OCR and search verification")

[Ask for help](/#consult) for a guided review of your Docker setup.
