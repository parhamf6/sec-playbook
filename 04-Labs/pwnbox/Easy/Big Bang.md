---
tags:
  - idor
---
https://pwnbox.io/challenges/big-bang
the core idea here is to bypass access control by changing the capitalization of the URL path.
normally, requesting `/attachments/3` gets denied because the attachment belongs to another user. but what if the authorization middleware checks the path case-sensitively while the router does not?
just change the path to uppercase:
```
/ATTACHMENTS/3
```
the middleware does not recognize it as a protected path, so the authorization check is skipped. the router still recognizes the route and serves the attachment.
main lesson here is that different layers of an application may handle paths differently, so you should test case variations when investigating access control issues.