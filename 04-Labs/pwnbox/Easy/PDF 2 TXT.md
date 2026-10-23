---
tags:
  - idor
  - upload
  - api
---
https://pwnbox.io/challenges/pdf-2-txt
this challenge is simple the main idea is capture request and test every thing. first i tested to change the main id :
`https://session.pwnbox-lab.com/job/407`
and i get  403, there was anther link to for downloading:
`https://3b421d15a176.pwnbox-lab.com/files/6`
tested and again 403.
then  i tried to upload a new file and i saw some thing interesting, in the requests there was this :
`https://3b421d15a176.pwnbox-lab.com/api/jobs/407/text` 
simply i changed the id few times and get into the  `api/jobs/403/text` and got the flag.