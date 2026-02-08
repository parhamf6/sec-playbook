When we are dealing with **blacklists**, we already know that some addresses will not be allowed to be sent using the `stockApi` parameter.

During testing, I found that `localhost` and `127.0.0.1` are blacklisted. Even when I only tried to add `/admin` to the URL, the request was blocked.  
So we need to do **two things**:

1. Find a way to access `localhost` without using a blacklisted address
2. Find a way to access `/admin` while bypassing the blacklist

The first part is easy. Sometimes we can ignore the zeros in the IP address, so instead of `127.0.0.1` we use: `127.1` 
This still points to localhost but bypasses the blacklist.

The second part is different. The `/admin` path is also blacklisted, so we need a way to hide it. Here we use something called **double URL encoding**.

How did I find this?

At first, I tried to URL-encode `admin`, but the request was still blocked. Then I encoded it **one more time**, and the request passed.

This suggests that something like this is happening in the backend:

`URL decode → blacklist check → passed → send request to backend → URL decode again → show results`

So the blacklist only decodes the URL **once**, while the backend decodes it again later. Because of that, the blacklist does not see `/admin`, but the backend does.
That is the bug here.
Final payload used in `stockApi`:
`http://127.1/%25%36%31%25%36%34%25%36%6d%25%36%39%25%36%6e/delete?username=carlos`

You only need to double-encode **one character** from `admin`, but I encoded all of them.
To do double encoding in Burp Suite:
- Select `admin`
- Right-click
- Convert selection → URL encode
- Repeat the encoding one more time