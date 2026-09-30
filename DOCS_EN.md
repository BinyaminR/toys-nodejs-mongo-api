# Toys API — English documentation

Base URL (local): `http://localhost:3001`
Auth header for protected routes: `x-api-key: <JWT>`

This document is for a frontend developer who needs to consume the API.

## 1. Authentication flow

1. `POST /users` — create an account.
2. `POST /users/login` — send email + password, receive `{ token, role, name }`.
3. Store the token.
4. On every POST / PUT / DELETE to `/toys`, send header `x-api-key: <token>`.

Do **not** send `Authorization: Bearer ...`. The course requirement is specifically `x-api-key`.

Missing header:

```json
{ "err": "You must send a token in header x-api-key" }
```

Invalid or expired token (60 minutes):

```json
{ "err": "Token invalid or expired" }
```

## 2. Users

### POST `/users` — register

Allowed body: `name` (2–99), `email` (valid, unique), `password` (6–99).
Not allowed: choosing `role` (always `USER`). Password is never stored as plain text.

```http
POST http://localhost:3001/users
Content-Type: application/json

{
  "name": "Dana Cohen",
  "email": "dana@gmail.com",
  "password": "123456"
}
```

Success 201 returns the user with `"password": "*****"`.
Duplicate email 400:

```json
{ "err": "Email already exists in system, try a different email" }
```

Joi errors return 400 with the details array.

### POST `/users/login`

Body: `email` and `password` only.

Success:

```json
{
  "token": "eyJ...",
  "role": "USER",
  "name": "Regular User"
}
```

Fail 401 (same message for unknown email and wrong password):

```json
{ "err": "Email or password is wrong" }
```

### GET `/users`

`{ "msg": "Users endpoint is working" }`

### GET `/users/userInfo`

Requires token. Returns the user document without `password`.

## 3. Toys

Pagination: page size is 10. `skip` is the page index starting at 0.
Server formula: `.skip(skip * 10).limit(10)`.

### GET `/toys`

General list. Bonus filters on the same route: `s`, `category`, `min`, `max`, `skip`.

```
GET http://localhost:3001/toys
GET http://localhost:3001/toys?skip=1
GET http://localhost:3001/toys?s=bear
GET http://localhost:3001/toys?category=Plush
GET http://localhost:3001/toys?min=10&max=40&skip=0
```

Response is a JSON array.

### GET `/toys/search?s=puzzle&skip=0`

Search in `name` or `info` (case-insensitive regex).

### GET `/toys/category/:catname?skip=0`

Example: `/toys/category/Educational`

### GET `/toys/prices?min=10&max=40&skip=0`

Uses `$gte` / `$lte`. Defaults: min 0, max 999999.

### GET `/toys/single/:id`

Returns one object. 404 if missing.

### GET `/toys/count`

```json
{ "count": 12 }
```

### POST `/toys` — token required

```http
POST http://localhost:3001/toys
Content-Type: application/json
x-api-key: YOUR_TOKEN

{
  "name": "Wooden Puzzle Animals",
  "info": "Colorful wooden puzzle with farm animals",
  "category": "Educational",
  "img_url": "https://images.pexels.com/photos/3662667/pexels-photo-3662667.jpeg",
  "price": 29
}
```

Allowed: `name`, `info`, `category`, `price`, optional `img_url`.
Not client-controlled: `user_id`, `_id`, timestamps.
201 + saved document.

### PUT `/toys/:id` — token required

Same body as POST. Owner or ADMIN only. Otherwise 403.

### DELETE `/toys/:id` — token required

No body. Same ownership rule as PUT.

## 4. Seed accounts

| Email | Password | Role |
| --- | --- | --- |
| admin@toys.com | 123456 | ADMIN |
| user@toys.com | 123456 | USER |

Categories: Educational, Outdoor, Plush (4 toys each). Seeded toys belong to `user@toys.com`.
