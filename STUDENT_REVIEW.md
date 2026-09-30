# Student review guide / מדריך לבדיקת ההגשה

Use this file to review the project against the original assignment before submitting the GitHub link.

השתמשו בקובץ הזה כדי לבדוק שהפרויקט ממלא את המשימה לפני שמגישים את קישור ה־GitHub.

---

## English — requirement map

### Part 1 — Toys collection (DB name TOYS)

| Assignment field | Implementation |
| --- | --- |
| name: string | `models/toyModel.js` |
| info: string | `models/toyModel.js` |
| category: string | `models/toyModel.js` |
| img_url: string (optional) | Joi allows empty / missing |
| price: number | Joi 1–999999 |
| date created | `timestamps: true` → `createdAt` |
| user_id: string | set in POST from token `_id` |

Database name is `TOYS` in `MONGO_URL` (`...mongodb.net/TOYS`).

### Part 1 — Routes (domain = localhost:3001)

| Route | File | Notes |
| --- | --- | --- |
| A GET `/toys?skip=` | `routes/toys.js` | limit 10, skip is page number * 10 |
| B GET `/toys/search?s=&skip=` | same | search name OR info |
| Bonus: merge search into A | GET `/toys?s=` | implemented |
| C GET `/toys/category/:catname?skip=` | same | |
| Bonus: category via query on A | GET `/toys?category=` | implemented |
| D POST `/toys` + user_id from token | same + `auth` | |
| E PUT `/toys/:id` only owner | same | ADMIN also allowed |
| F DELETE `/toys/:id` only owner | same | ADMIN also allowed |
| G GET `/toys/prices?min=&max=&skip=` | same | `$gte` / `$lte` |
| H GET `/toys/single/:id` one object | same | |
| I GET `/toys/count` | same | `{ count }` |

### Part 2 — Users + security

| Assignment rule | Implementation |
| --- | --- |
| Users collection: name, email, password, date, role | `models/userModel.js` |
| role ADMIN or USER, default USER | default in schema + forced on register |
| email UNIQUE + error on duplicate | unique index + `err.code === 11000` |
| POST `/users` register | `routes/users.js` |
| password hashed with bcrypt | `bcrypt.hash(..., 10)` |
| POST `/users/login` body email + password | `validLogin` |
| compare with bcrypt | `bcrypt.compare` |
| return TOKEN | `createToken(_id, role)` |
| toys.user_id links user to toy | set on POST |
| POST/PUT/DELETE toys require token header `x-api-key` | `middlewares/auth.js` |
| invalid token → JSON error | 401 messages |
| Joi + bcrypt + JWT | used in the correct places |
| TOKEN_SECRET and connection in ENV | `config/secret.js` + `.env.example` |
| do not upload .env or node_modules | `.gitignore` |
| ~12 toys, 3 categories | `seed/seed.js` |
| documentation | README + DOCS_EN + DOCS_HE + `/` HTML |

### Local review (15 minutes)

1. `cp .env.example .env` and put a real `MONGO_URL` + `TOKEN_SECRET`.
2. `npm install`
3. `npm run seed`
4. `npm start`
5. Open `/` and read the docs page.
6. Thunder Client: GET `/toys`, GET `/toys/count`, search, category, prices, login, POST toy with token, PUT/DELETE ownership, 401 without token, 403 as another user.
7. `git check-ignore .env` should print `.env`.

---

## עברית — מפת דרישות

כל השדות מהמשימה קיימים ב־`models/toyModel.js` ו־`models/userModel.js`.
תאריך יצירה מגיע מ־`timestamps: true`.
`user_id` נשמר אוטומטית מהטוקן בזמן POST.

כל תשע הכתובות מהמשימה ממומשות, כולל הבונוסים (חיפוש וקטגוריה גם ב־GET `/toys`).

`skip` הוא מספר עמוד לפי 10 צעצועים בדף (מתחיל מ־0).

אבטחה: Joi, bcrypt, JWT, middleware, כותרת `x-api-key`, מייל ייחודי, סודות רק ב־ENV.

לפני ההגשה: למלא `.env` מקומי בלבד, להריץ seed+start, לבדוק ב־Thunder Client, ולוודא שב־GitHub אין `.env` ואין `node_modules`.
