---
tags:
  - recon
---

https://pwnbox.io/challenges/backup-drawer
this challenge is very simple and yet important.
in the about section they say that the company would keep the backups but they say to crawlers that don't visit and don't index it. so where we give this instructions to crawlers? yes robots.txt
when you visit the robots.txt you can find out the directory of the backup files, and after downloading the porta-backup.zop you can inspect the files and find the flag.