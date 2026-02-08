For this SQL injection lab, we're targeting an Oracle database. The key difference with Oracle is that **every SELECT statement must include a FROM clause**, unlike other databases. This means we need to use a special dummy table called `dual` during our testing phase.

`dual` is a default Oracle table with one row and one column. It's perfect for testing queries when we don't actually need real data.

---

### Step-by-Step Exploitation

#### **Step 1: Confirm SQL Injection Vulnerability**
First, I test if the category parameter is vulnerable by adding a single quote:
```
' 
```
If the application returns an error or behaves unexpectedly, SQL injection is likely possible.

#### **Step 2: Determine Number of Columns (Using UNION)**
I need to find out how many columns the original query returns. I use `UNION SELECT` with `FROM dual`:
```
' UNION SELECT NULL FROM dual--
```
If this fails, I add more NULLs until it succeeds:
```
' UNION SELECT NULL, NULL FROM dual--
```
**Success!** This means the original query returns **2 columns**.

*Why use `dual` here?* Because Oracle requires a FROM clause, and `dual` lets me test the UNION structure without needing to know any real table names yet.

#### **Step 3: Check Column Data Types**
Now I need to see which columns accept text data (since version info is text):
```
' UNION SELECT 'abc', NULL FROM dual--
```
If this works, column 1 accepts text. Then test column 2:
```
' UNION SELECT NULL, 'abc' FROM dual--
```
Both work, so **both columns accept text data**.

#### **Step 4: Query the Database Version**
Oracle stores version information in a system view called `v$version`. Since I now know the query structure (2 text columns), I can query it:
```
' UNION SELECT BANNER, NULL FROM v$version--
```
Here, `BANNER` is the column name in `v$version` that contains version strings, and `NULL` fills the second column to match the original query's structure.

---

### Complete Exploit Payload

The final working payload for the category parameter:
```
' UNION SELECT BANNER, NULL FROM v$version--
```

### Result

When injected, this payload causes the application to:
1. Execute the original product category query
2. UNION it with our version query
3. Return Oracle database version information in the response

The version string appears somewhere in the page (often in error messages or displayed data), completing the lab objective of displaying the database version.