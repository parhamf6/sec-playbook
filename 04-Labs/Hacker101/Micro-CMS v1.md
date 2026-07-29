# Flag 0
for this based on the hint i created a page, nothing interesting, until i saw the id of the page, so i tested few pages from 0 to number of my own page that i created i get forbidden for one and two of them was just get open that mean they were public and other i get 404 so there are not there. in the forbidden one i tried to go to edit mode, i got to the edit mode on my own page then change the id and boom, the flag was there, simple IDOR.
# Flag 1
this one is kind of fun, the id of the page maybe work as a SQL query, some thing like this :
```sql
SELECT * FROM pages WHERE id='$user-input'
```
so you could try to break it and get every thing out of it :
```sql
SELECT * FROM pages WHERE id='1' or 1=1--
```
that would give you the flag.
# Flag 2
when i created a new page and watched the source on the new item, its look like that they would put it right there, without any sanitizing, so i tested for XSS. i put this on the title and the text of a new page.
```html
hello<script>alert(1);</script>
```
nothing happens in the page of that post but lets see the home and boom you got it.
# Flag 3
still its very interesting that we don't see ant sanitation of the text of the page so i tried another thing and add a button to it :
```html
<button onclick=alert(‘xss’)>click</button>
```
so when we make the page and click on the button, and then see the source there is the flag.