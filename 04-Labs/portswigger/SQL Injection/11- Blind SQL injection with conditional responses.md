This lab has a **blind SQL injection** vulnerability. That means I can inject SQL code, but I don't see the results directly - no error messages, no data displayed. The only clue I get is whether the query returns any rows or not.

---

### How It Works
The application uses a tracking cookie. When it processes this cookie, it runs a SQL query with it. If the query returns results, the page shows "Welcome back". If it doesn't, that message disappears.

So I can ask the database yes/no questions:
- If "Welcome back" appears → My condition was TRUE
- If "Welcome back" disappears → My condition was FALSE

---

### Step-by-Step Attack

**Step 1: Confirm the vulnerability**
First, I test if I can control the SQL query:

```
TrackingId=xyz' AND '1'='1
```
→ "Welcome back" appears (TRUE condition)

```
TrackingId=xyz' AND '1'='2  
```
→ "Welcome back" disappears (FALSE condition)

This confirms I can inject SQL and see the results indirectly.

**Step 2: Check if the users table exists**
```
TrackingId=xyz' AND (SELECT 'a' FROM users LIMIT 1)='a
```
If "Welcome back" appears, there's a `users` table.

**Step 3: Check if administrator exists**
```
TrackingId=xyz' AND (SELECT 'a' FROM users WHERE username='administrator')='a
```
If "Welcome back" appears, there's an `administrator` user.

---

**Step 4: Find the password length**
I need to guess how long the password is, character by character:

```
TrackingId=xyz' AND (SELECT 'a' FROM users WHERE username='administrator' AND LENGTH(password)>1)='a
```
If true, password is longer than 1 character.

I keep testing:
```
LENGTH(password)>2
LENGTH(password)>3
LENGTH(password)>4
...
```

When I test `LENGTH(password)>20` and "Welcome back" disappears, I know the password is **exactly 20 characters long**.

---

**Step 5: Find each character of the password**
Now the slow part - I need to guess each of the 20 characters. For each position (1 through 20), I test every possible character (a-z, 0-9).

For position 1:
```
TrackingId=xyz' AND (SELECT SUBSTRING(password,1,1) FROM users WHERE username='administrator')='a
```
Test 'a', 'b', 'c'... until "Welcome back" appears.

For position 2:
```
TrackingId=xyz' AND (SELECT SUBSTRING(password,2,1) FROM users WHERE username='administrator')='a
```
And so on...

---

### Using Burp Suite to Automate This
Doing this manually would take 20 positions × 36 characters = 720 requests! So I use Burp Intruder:

1. **Send to Intruder** the request with my cookie
2. **Set the payload position** around the character I'm testing:
   ```
   TrackingId=xyz' AND (SELECT SUBSTRING(password,1,1) FROM users WHERE username='administrator')='§a§
   ```
3. **Configure payloads**: a-z and 0-9
4. **Add "Welcome back" to Grep** so I can easily see which request succeeded
5. **Run the attack** for position 1
6. **Repeat** for positions 2-20

---

### Example of What I'm Doing
Let's say after testing, I find:
- Position 1: 's' makes "Welcome back" appear
- Position 2: 'e' makes "Welcome back" appear  
- Position 3: 'c' makes "Welcome back" appear
- Position 4: 'r' makes "Welcome back" appear
...

Eventually I get the full password, let's say: `secretpassword12345`

---

### Step 6: Log in
Go to the login page, enter:
- Username: `administrator`
- Password: `[what I found]`

---

### Why This Is Called "Blind" SQL Injection
- **Blind** = I can't see the results directly
- I only get a hint (Welcome back message)
- I have to **infer** information by asking carefully crafted true/false questions
- It's like playing "20 questions" with the database

### The Clever Part:
Even though I never see the password directly, by asking "Is the first character 'a'?", "Is the first character 'b'?", etc., I can slowly reconstruct it one character at a time. Each answer gives me one bit of information until I have the complete password.