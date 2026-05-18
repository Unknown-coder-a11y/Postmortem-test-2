# Postmortem-test-2
virtual host test.

Date: 2026-05-17
Duration: ~1 hour
Severity: Low (learning environment)

## What I Was Trying to Do
Set up two virtual hosts on nginx
site1.local and site2.local serving
different webpages on same server

## What Happened
site1.local worked perfectly
site2.local returned 403 Forbidden

## Timeline
23:36 - Created site configs and enabled them
23:36 - nginx -t failed → found "lsiten" typo in site2 config

23:57 - Fixed lsiten typo, nginx reloaded
23:57 - site1.local working, site2.local still 403
01:11 - Checked permissions with ls -la
01:11 - Ran namei -l to trace full path permissions
01:11 - Tried chmod 755 and chown www-data → still 403
01:18 - Found root cause → file named "inde.html" not "index.html"
01:18 - Renamed file with mv command → site2.local working

## Root Cause
Filename typo: inde.html instead of index.html
nginx could not find index file so returned 403

## What I Tried That Didn't Work
chmod 755 on folder → didn't fix it
chmod 644 on file → didn't fix it
chown www-data → didn't fix it
These were all valid things to check but not the root cause

## What Actually Fixed It
sudo mv /var/www/site2/inde.html /var/www/site2/index.html

## What I Learned
1. nginx -t catches config syntax errors before they break things
2. 403 Forbidden doesn't always mean permissions — check filename too
3. namei -l is useful for tracing permission issues through full path
4. Always verify the actual filename matches what config expects
5. Systematic debugging means checking everything, not just the obvious

## What Would Have Caught This Earlier
Running ls /var/www/site2/ immediately when 403 appeared
Would have seen inde.html right away and saved 1 hour
