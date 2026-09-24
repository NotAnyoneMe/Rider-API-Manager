# Authentication

The API uses **JWT (JSON Web Tokens)** for authentication. There are two types of users: **Admins** and **Riders**. Each has different permissions.

---

## Login

**`POST /auth/login`**

Both admins and riders use the same login endpoint. The API figures out who you are and returns the right token.

**Request (form data):**

```
username: john_rider
password: mypassword
```

**Response:**

```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIs...",
  "token_type": "bearer",
  "role": "rider"
}
```

The `role` field tells you whether the logged-in user is an `admin` or a `rider`.

---

## Using the Token

Include the token in every request as a header:

```
Authorization: Bearer eyJhbGciOiJIUzI1NiIs...
```

Tokens expire after **24 hours**. After that, the user needs to log in again.

---

## Roles & Permissions

| Action              | Admin | Rider                |
| ------------------- | ----- | -------------------- |
| Create riders       | ✅    | ❌                   |
| View all riders     | ✅    | ❌                   |
| View own profile    | ✅    | ✅                   |
| Update rider info   | ✅    | ❌                   |
| Delete rider        | ✅    | ❌                   |
| Update own location | ❌    | ✅ (own only)        |
| Update order status | ❌    | ✅ (own orders only) |
| Add daily stats     | ✅    | ❌                   |
| Approve time off    | ✅    | ❌                   |
| Request time off    | ✅    | ✅ (own only)        |
| Add restaurants     | ✅    | ❌                   |
| View logs           | ✅    | ❌                   |

---

## Security Notes

- Passwords are hashed using **bcrypt** — never stored in plain text
- Tokens are signed with a secret key using **HS256**
- Each token encodes the user's ID and type (`admin` or `rider`)
- Riders cannot access other riders' data — the API enforces this on every request
