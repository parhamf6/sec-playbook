https://pwnbox.io/challenges/loose-lips
this one is kind of fun. IDOR and user session if you connect this dots, so you looking for some kind of Id in the user session.
first login and look at the data.
when we see the source codes, in the debugger you can find a file called team.js, there is a API endpoint that looks interesting lets make a call to it. either you can use burp or just give that address into you url bar in browser :
```
https://your-session.pwnbox-lab.com/api/teamMemberInfo/1
```
and then you have the flag:
```json
{"id":1,"username":"mira","name":"Mira Patel","email":"mira@acme.intra","title":"CTO","secret":"pwnbox{flag}"}
```
the reason that i tested the id = 1 is just most of the time first user is admin, but if it was not you have the endpoint you can enumerate user ids.
