---
tags:
  - idor
---
https://pwnbox.io/challenges/team-sync
in this lab, the main idea is that check the requests, in one of those requests we see this :
`https://session.pwnbox-lab.com/api/users/logged-in-user-id`
so we can check that what if we change the username from our own to some thing else?
so go to the team page and check each of the usernames one by one and then you got the flag.