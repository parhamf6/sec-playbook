---
description: solving the Postbook on the Hacker101 CTF
tags:
  - xss
  - idor
---

# Flag 0
i tried to log in to the users account, based on the hint.
for the user that has the username = user the password is password.
and done you got the flag.
# Flag 1
based on the hint, there is some thing like IDOR, so go to a post and view some thing then change the id and you can get the flag in the id=2 .
# Flag 2
for this based on the hint i get into the form and used the inspect then some thing was interesting :
```html
<input type="hidden" name="user_id" value="3" />
```
so i tried changed it to value="1" and then create a post and got it we have the flag.
# Flag 3 
actually its easy , the 185 * 5 = 945 , so i got the idea that what if some thing or some one has this id, so i first tried to user with this id and nothing, but i saw id in the posts too so i checked it and done.
# Flag 4
pretty fun, you can go to the form that would edit your own post then change it to any other id and then you see that you can see the content and edit them, you can edit any of them, i edited the post 2 and done we have the flag after submit the edit.
# Flag 5
the hint is clear, we should change the cookie and make the system think that we have the cookie of the user with id=1.
so i tried to check the cookie and one interesting value, id="`eccbc87e4b5ce2fe28308fd9f2a7baf3`", first i tried to decode it from base64(rookie and bad mistake) because its 32 hex not 64 so its not, then i thought about md5, we can not decode it , so i guessed that it would be id and yes:
```bash
echo -n 3 | md5sum
eccbc87e4b5ce2fe28308fd9f2a7baf3
```
so lets try and get the md5 value of the 1 it would be : `c4ca4238a0b923820dcc509a6f75849b` and replace it in the cookie and done.
# Flag 6
still the delete take the md5 of the id of that post. so you just give the md5 id of that the first post `c4ca4238a0b923820dcc509a6f75849b` and done you have the flag.
