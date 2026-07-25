https://pwnbox.io/challenges/baby-ssrf
the goal of this lab is to tell you about other forms that represent the same thing.
in this lab if you just go there and put `127.0.0.1` or `localhost` you would get block, because there are internals. but if the block list is not good enough it would not block other forms of localhost like this ones:
```
http://0/
http://127.1
http://127.0.1
```
and done just request to one of this and the given port (9999) and you got the flag.