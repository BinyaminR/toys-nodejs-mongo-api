# Toys API — תיעוד בעברית

כתובת מקומית: `http://localhost:3001`
כותרת אבטחה לראוטים מוגנים: `x-api-key: <JWT>`

המסמך הזה מיועד למתכנת צד לקוח שצריך לעבוד מול ה־API.

## 1. תהליך התחברות

1. `POST /users` — יצירת משתמש.
2. `POST /users/login` — שולחים אימייל וסיסמה, מקבלים `{ token, role, name }`.
3. שומרים את הטוקן.
4. בכל POST / PUT / DELETE ל־`/toys` שולחים כותרת `x-api-key`.

לא שולחים `Authorization: Bearer`. בדרישות הקורס הכותרת היא בדיוק `x-api-key`.

בלי כותרת:

```json
{ "err": "You must send a token in header x-api-key" }
```

טוקן לא תקין או שפג תוקף (60 דקות):

```json
{ "err": "Token invalid or expired" }
```

## 2. משתמשים

### POST `/users` — הרשמה

מה מותר ב־BODY: `name`, `email`, `password`.
`role` תמיד נשמר כ־`USER`. הסיסמה לא נשמרת כטקסט גלוי.
אימייל שכבר קיים מחזיר 400:

```json
{ "err": "Email already exists in system, try a different email" }
```

### POST `/users/login`

ב־BODY רק `email` ו־`password`. הצלחה מחזירה טוקן.

### GET `/users/userInfo`

חובה טוקן. מחזיר את המשתמש בלי סיסמה.

## 3. צעצועים

עימוד: בכל עמוד 10 צעצועים. `skip` הוא מספר העמוד שמתחיל מ־0.

| שיטה | כתובת | הסבר |
| --- | --- | --- |
| GET | `/toys?skip=0` | ראוט א' + בונוס `s` / `category` / `min` / `max` |
| GET | `/toys/search?s=&skip=0` | ראוט ב' — חיפוש ב־name או info |
| GET | `/toys/category/:catname?skip=0` | ראוט ג' |
| POST | `/toys` | ראוט ד' — חובה טוקן, שומר user_id |
| PUT | `/toys/:id` | ראוט ה' — בעלים או ADMIN |
| DELETE | `/toys/:id` | ראוט ו' |
| GET | `/toys/prices?min=10&max=40&skip=0` | ראוט ז' |
| GET | `/toys/single/:id` | ראוט ח' — אובייקט אחד |
| GET | `/toys/count` | ראוט ט' |

גוף צעצוע:

```json
{
  "name": "פאזל עץ",
  "info": "פאזל צבעוני עם חיות",
  "category": "Educational",
  "img_url": "https://images.pexels.com/photos/3662667/pexels-photo-3662667.jpeg",
  "price": 29
}
```

מה אסור לשלוט בו: `user_id` (מגיע מהטוקן).

## 4. נתוני דמו

- `admin@toys.com` / `123456` — ADMIN
- `user@toys.com` / `123456` — USER

3 קטגוריות, 12 צעצועים: Educational, Outdoor, Plush.
