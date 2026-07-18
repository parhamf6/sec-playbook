> Whenever I'm dealing with an Access Control challenge, I first go into **recon mode**. I don't start guessing vulnerabilities right away. Instead, I follow a simple checklist of places that commonly leak sensitive information.

- `robots.txt`
- `sitemap.xml`
- Common admin endpoints
- JavaScript files
- Hidden links in the application

The first thing I checked was `robots.txt`, and it revealed the admin panel.
and we get this :
```text
User-agent: *
Disallow: /administrator-panel
```
done. go to the /administrator-panel and delete carlos.