This lab teaches the first step of a UNION attack - figuring out how many columns a SQL query returns. This is important because for a UNION attack to work, our injected query must return exactly the same number of columns as the original query.

---

### How UNION Attacks Work
A UNION combines results from two SELECT statements, but they must match in:
1. Number of columns
2. Data types of columns

If they don't match, we get an error. We can use this to our advantage to discover the structure.

---

### Step-by-Step Method

**Step 1: Start testing with one NULL**
I add this to the category parameter:
```
' UNION SELECT NULL--
```
If I get an error (which I will), it means the original query has more than 1 column.

**Step 2: Add another NULL**
I try with two columns:
```
' UNION SELECT NULL, NULL--
```
If I still get an error, I continue adding NULLs.

**Step 3: Keep adding until it works**
```
' UNION SELECT NULL, NULL, NULL--
' UNION SELECT NULL, NULL, NULL, NULL--
```
And so on...

---

### When Do I Know It Works?
When the page loads normally without errors and shows extra content. The NULL values might appear somewhere on the page, or the page might just look different but not broken.

For example, if this works:
```
' UNION SELECT NULL, NULL, NULL--
```
Then I know the original query returns **3 columns**.

---

### Why Use NULL?
NULL works because:
- It's compatible with any data type (text, number, date, etc.)
- Every database supports NULL
- It doesn't cause type conversion errors

---

### Complete Testing Process:
1. `' UNION SELECT NULL--` → ERROR (more than 1 column)
2. `' UNION SELECT NULL, NULL--` → ERROR (more than 2 columns)  
3. `' UNION SELECT NULL, NULL, NULL--` → SUCCESS! (found it - 3 columns)

Once I find the right number, the page loads normally and I've completed the lab.

---

### What's Next?
Knowing the column count is just step one. In real attacks, I'd then:
1. Figure out which columns accept text data
2. Start extracting actual data from the database
3. Find sensitive information like usernames and passwords

But for this lab, just finding the column count is enough to solve it.