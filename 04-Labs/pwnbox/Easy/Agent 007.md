---
tags:
  - idor
---
https://pwnbox.io/challenges/agent-007
this one is kind of simple too. in the first view we have some kind of share link that uses uuid4, so guessing that would not be easy at all. but some thing looked interesting in the footer :
```text
Registry **v2** — every dossier is now handed out as a random share link instead of being passed round by its number.
```
so one questions popped into my head, what if there is two system and the old system was working and better what if the  changing from the old system to new system was not complete and they had to keep it? so done, i tested this :
`https://agent007-session.pwnbox-lab.com/share/7`
and got the flag.