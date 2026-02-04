This lab has a SQL injection vulnerability where we can use a UNION attack to get information from the database. The goal is to find out what database version is running.

---

### Step 1: Test if it's vulnerable
First, I add a single quote `'` to the category parameter to see if it breaks the query. If the page shows an error or behaves strangely, we know SQL injection is possible.

---

### Step 2: Figure out how many columns there are
I need to know how many columns the original query returns so my UNION attack will match. I start testing with:

```
' UNION SELECT NULL--
```

If that doesn't work (gives an error), I try with more NULLs:

```
' UNION SELECT NULL, NULL--
```

When this works without errors, I know there are **2 columns** in the original query.

**Note:** Unlike Oracle, MySQL and Microsoft SQL don't need a `FROM` clause for simple SELECT statements, so we can test without using a dummy table.

---

### Step 3: Check what type of data the columns accept
Next, I test which columns can hold text (since version information is text):

```
' UNION SELECT 'abc', NULL--
```

If that works, the first column accepts text. Then I test the second column:

```
' UNION SELECT NULL, 'abc'--
```

Both work, so both columns can hold text data.

---

### Step 4: Get the database version
For MySQL and Microsoft SQL Server, we use `@@version` to get the version information. Since we need to match the 2-column structure:

```
' UNION SELECT @@version, NULL--
```

This puts the version in the first column and NULL in the second column.

---

### **Important: The Comment Character**
I found something interesting when testing - using the browser directly didn't work, but using Burp Suite did. Here's why:

In MySQL, we use `#` to comment out the rest of the query. But in a browser URL, `#` has a special meaning (it's for page anchors).

**What worked in Burp:**
```
'+UNION+SELECT+@@version,+NULL#
```

**For browser testing, you might need to use:**
```
' UNION SELECT @@version, NULL%23
```
(The `%23` is the URL-encoded version of `#`)

Or you can use the alternative comment syntax:
```
' UNION SELECT @@version, NULL-- 
```
(Notice the space after `--`)

---

### Why This Works
When we inject this payload:
1. The original query runs first
2. Our `UNION SELECT @@version, NULL` appends to it
3. The database executes both queries together
4. The version information appears somewhere on the page (often in an error message or displayed data)

The `@@version` variable contains the exact database version string, which tells us whether it's MySQL, Microsoft SQL Server, and what version number.

---

### Final Payload
For this lab, use either:
- In Burp Suite: `'+UNION+SELECT+@@version,+NULL#`
- In browser URL: `' UNION SELECT @@version, NULL%23`

Once injected successfully, the database version will appear in the response, completing the lab.