---
tags:
  - ssrf
---

https://pwnbox.io/challenges/flag-file
it is the core idea behind the SSRF lets the server(some thing inside the local/trusted network) make the request for you. 
based on the title we can guess that the flag is a file on the server, most of the times you can not access the file directly but what if you make the server it self fetch that for you? and done just give the file url schema to it:
```
file:///flag.txt
```
main lesson here is we have more then one url schema so you should test them.