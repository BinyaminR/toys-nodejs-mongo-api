# Toys REST API — Node.js + MongoDB

Full server-side project for a toys catalog.

- **Stack:** Node.js, Express, MongoDB (Mongoose), Joi, bcrypt, JWT
- **Port:** `3001`
- **Database name:** `TOYS`
- **Auth header:** `x-api-key`
- **Docs site (after `npm start`):** http://localhost:3001

This repository is ready to submit: no `.env`, no `node_modules`, secrets stay local.

More docs:
- English API reference: [DOCS_EN.md](DOCS_EN.md)
- Hebrew API reference: [DOCS_HE.md](DOCS_HE.md)
- Requirement checklist for the student: [STUDENT_REVIEW.md](STUDENT_REVIEW.md)

---

# English

## What this API does

Registered users can add a toy (`name`, `info`, `category`, `img_url`, `price`).
Anyone can read toys with pagination, search, category filter, price range, single item, and count.
Add / edit / delete require a valid JWT in header `x-api-key`.
The server stores `user_id` from the token. A user can edit/delete only their own toys. An `ADMIN` can edit/delete any toy.

## Project structure

```
app.js                 # Express server
config/secret.js       # reads PORT, MONGO_URL, TOKEN_SECRET from .env
db/mongoConnect.js     # mongoose connection
models/userModel.js    # users schema + Joi + JWT
models/toyModel.js     # toys schema + Joi
middlewares/auth.js    # auth + authAdmin
routes/users.js        # register / login / userInfo
routes/toys.js         # all toys endpoints
seed/seed.js           # 2 demo users + 12 toys / 3 categories
public/index.html      # live API documentation (EN + HE)
```

## Install and run locally

1. Create a free cluster on MongoDB Atlas (or use local MongoDB).
2. Create a database user and allow network access (`0.0.0.0/0` for testing).
3. Copy the connection string. The database name in the URL should be `TOYS`.

```bash
git clone https://github.com/BinyaminR/toys-nodejs-mongo-api.git
cd toys-nodejs-mongo-api
npm install
cp .env.example .env
```

Edit `.env`:

```
PORT=3001
MONGO_URL=mongodb+srv://USERNAME:PASSWORD@CLUSTER.mongodb.net/TOYS
TOKEN_SECRET=put_a_long_random_string_here
```

```bash
npm run seed
npm start
```

Server: http://localhost:3001
Interactive docs: http://localhost:3001

### Demo users created by seed

| Email | Password | Role |
| --- | --- | --- |
| admin@toys.com | 123456 | ADMIN |
| user@toys.com | 123456 | USER |

12 toys, 3 categories: **Educational**, **Outdoor**, **Plush**.

## Users API

Base: `http://localhost:3001/users`

### POST `/users` — register

```json
{
  "name": "Dana Cohen",
  "email": "dana@gmail.com",
  "password": "123456"
}
```

- Joi validation
- bcrypt hash (10 rounds)
- role forced to `USER`
- unique email — duplicate returns 400 JSON error
- password returned as `*****`

### POST `/users/login`

```json
{
  "email": "dana@gmail.com",
  "password": "123456"
}
```

Returns `{ token, role, name }`. Send the token as header `x-api-key`.

### GET `/users/userInfo`

Requires token. Returns the user without password.

## Toys API

`skip` is the page number starting at 0. Limit is always 10.

| Method | Route | Notes |
| --- | --- | --- |
| GET | `/toys?skip=0` | List. Bonus: `s`, `category`, `min`, `max` |
| GET | `/toys/search?s=ball&skip=0` | Search name OR info |
| GET | `/toys/category/:catname?skip=0` | By category |
| GET | `/toys/prices?min=10&max=40&skip=0` | Price range |
| GET | `/toys/single/:id` | One object |
| GET | `/toys/count` | `{ count }` |
| POST | `/toys` | Token required. Sets `user_id` |
| PUT | `/toys/:id` | Token + owner or ADMIN |
| DELETE | `/toys/:id` | Token + owner or ADMIN |

Toy body (POST / PUT):

```json
{
  "name": "Soccer Ball Size 4",
  "info": "Durable outdoor soccer ball for kids",
  "category": "Outdoor",
  "img_url": "https://images.pexels.com/photos/47730/the-ball-stadion-football-the-pitch-47730.jpeg",
  "price": 25
}
```

Missing token → 401. Wrong owner → 403.

## Security

- Joi, bcrypt, JWT
- Auth middleware on POST / PUT / DELETE
- Header name: `x-api-key`
- TOKEN_SECRET and MONGO_URL only in `.env`
- `.gitignore` blocks `.env` and `node_modules`

## Deploy on Render

1. New Web Service from this repo
2. Build: `npm install`
3. Start: `node app.js`
4. Env vars: `MONGO_URL`, `TOKEN_SECRET`
5. Optional: run `node seed/seed.js` once

---

# עברית

## מה המערכת עושה

כל משתמש רשום יכול להוסיף צעצוע עם שם, תיאור, קטגוריה, תמונה ומחיר.
אפשר לשלוף צעצועים לפי עמודים, חיפוש, קטגוריה, טווח מחירים, רשומה בודדת ומספר רשומות.
הוספה / עריכה / מחיקה דורשות טוקן תקין בכותרת `x-api-key`.
השרת שומר `user_id` מתוך הטוקן. משתמש רגיל יכול לערוך/למחוק רק צעצועים שלו. `ADMIN` יכול לערוך/למחוק הכל.

## התקנה והרצה

```bash
npm install
cp .env.example .env
```

למלא ב־`.env` את `MONGO_URL` ואת `TOKEN_SECRET`, ואז:

```bash
npm run seed
npm start
```

השרת: http://localhost:3001

### משתמשי דמו

- `admin@toys.com` / `123456` — ADMIN
- `user@toys.com` / `123456` — USER

12 צעצועים, 3 קטגוריות: Educational, Outdoor, Plush.

## ראוטים

`skip` = מספר עמוד שמתחיל מ־0. כל עמוד = 10 צעצועים.

| שיטה | כתובת | הסבר |
| --- | --- | --- |
| GET | `/users` | בדיקה |
| POST | `/users` | הרשמה. סיסמה מוצפנת. מייל ייחודי |
| POST | `/users/login` | התחברות + JWT |
| GET | `/users/userInfo` | פרטי המשתמש. חובה טוקן |
| GET | `/toys?skip=0` | ראוט א' + בונוס `s` / `category` / `min` / `max` |
| GET | `/toys/search?s=&skip=0` | ראוט ב' |
| GET | `/toys/category/:catname?skip=0` | ראוט ג' |
| POST | `/toys` | ראוט ד' — חובה טוקן |
| PUT | `/toys/:id` | ראוט ה' — בעלים או ADMIN |
| DELETE | `/toys/:id` | ראוט ו' — בעלים או ADMIN |
| GET | `/toys/prices?min=&max=&skip=0` | ראוט ז' |
| GET | `/toys/single/:id` | ראוט ח' |
| GET | `/toys/count` | ראוט ט' |

כותרת הטוקן: `x-api-key`
