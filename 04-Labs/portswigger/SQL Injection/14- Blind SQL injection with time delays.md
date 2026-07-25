This lab has a different type of blind SQL injection. This time, there's no visual feedback at all - no "Welcome back" message, no error messages, nothing changes on the page regardless of what my query does.

But I can still exploit it by using **time delays**.

---

### How Time-Based Blind SQL Injection Works

The idea is simple: I ask the database to wait before responding. If it waits, I know my SQL injection worked. The delay acts as my "signal" from the database.

**The basic concept:**
1. I inject: `' || pg_sleep(10)--`
2. If the injection works → Database sleeps for 10 seconds → Page takes 10+ seconds to load
3. If the injection fails → No sleep → Normal page load time

---

### Step-by-Step Solution

**Step 1: Identify the database type**
Different databases have different sleep commands:
- PostgreSQL: `pg_sleep(10)`
- MySQL: `SLEEP(10)` or `BENCHMARK(10000000,MD5('a'))`
- Microsoft SQL: `WAITFOR DELAY '0:0:10'`
- Oracle: `dbms_pipe.receive_message(('a'),10)`

For this lab, it's PostgreSQL (as shown in the solution).

**Step 2: Craft the payload**
I take my TrackingId cookie and add the sleep command:
```
TrackingId=x'||pg_sleep(10)--
```

Breaking this down:
- `x'` - Closes the original string
- `||` - SQL concatenation (or sometimes `+` in other databases)
- `pg_sleep(10)` - PostgreSQL command to sleep for 10 seconds
- `--` - Comments out the rest of the query

**Step 3: Send the request**
When I send this modified cookie, I watch the response time. Instead of the usual 1-2 seconds, the page should take about 10 seconds to load.

---

### What's Happening in the Database
The original query might look like:
```sql
SELECT * FROM tracking WHERE tracking_id = 'xyz'
```

After my injection:
```sql
SELECT * FROM tracking WHERE tracking_id = 'x'||pg_sleep(10)--'
```

Which becomes:
```sql
SELECT * FROM tracking WHERE tracking_id = 'x' || pg_sleep(10)
```

The database concatenates 'x' with the result of `pg_sleep(10)`. Since `pg_sleep(10)` returns nothing, it's essentially just running the sleep function.

---

### Why This Is Useful
Even though this lab just asks for a simple delay, in real attacks, time delays let me ask true/false questions:

**Example: Asking if the administrator password starts with 'a'**
```
TrackingId=x'||(SELECT CASE WHEN (SUBSTRING(password,1,1)='a') THEN pg_sleep(10) ELSE pg_sleep(0) END FROM users WHERE username='administrator')--
```

- If password starts with 'a' → 10 second delay
- If password doesn't start with 'a' → No delay

I can use this to extract data character by character, just like the previous lab, but using timing instead of a "Welcome back" message.

---

### Testing the Payload
When I send:
```
TrackingId=x'||pg_sleep(10)--
```

I should see:
- **Before:** Page loads in 1-2 seconds
- **After:** Page loads in 10-11 seconds

The extra time confirms my SQL injection worked and completes the lab.

---

### Different Database Syntax Examples:
If this were other databases:
- **MySQL:** `' || SLEEP(10)--`
- **Microsoft SQL:** `' || WAITFOR DELAY '0:0:10'--`  
- **Oracle:** `' || dbms_pipe.receive_message(('a'),10)--`

### Important Note:
Time-based blind SQL injection is very slow. Each character of data might require multiple requests with different sleep times. A 20-character password could take 20 × 10 seconds × 36 tests = 2 hours! That's why in real attacks, hackers often use shorter sleep times or more efficient methods.