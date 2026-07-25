this idea work every where and its the first step :
> Whenever I'm dealing with an Access Control challenge, I first go into **recon mode**. I don't start guessing vulnerabilities right away. Instead, I follow a simple checklist of places that commonly leak sensitive information.

so lets go see this list :
- `robots.txt`
- `sitemap.xml`
- Common admin endpoints
- JavaScript files
- Hidden links in the application
  
if you check one by one you can when you go to the debugger and open the page source code, you can see a interesting code :
```js
var isAdmin = false;
if (isAdmin) {
   var topLinksTag = document.getElementsByClassName("top-links")[0];
   var adminPanelTag = document.createElement('a');
   adminPanelTag.setAttribute('href', '/admin-jwstc3');
   adminPanelTag.innerText = 'Admin panel';
   topLinksTag.append(adminPanelTag);
   var pTag = document.createElement('p');
   pTag.innerText = '|';
   topLinksTag.appendChild(pTag);
}
```
so basically its says hey if user isAdmin is true then you can show a link on the header and the link is `admin-jwstc3` (for me) and done.