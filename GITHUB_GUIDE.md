# העלאה ל־GitHub של הסטודנט / Upload to the student's own GitHub

ההגשה חייבת להיות **ריפו ציבורי בחשבון GitHub של הסטודנט**.
הריפו הזה הוא רק דוגמה / רפרנס:

https://github.com/BinyaminR/toys-nodejs-mongo-api

לא מגישים את הקישור הזה. מגישים את הקישור של הריפו **שלכם**.

## עברית — מדריך העלאה

1. חשבון GitHub פעיל.
2. Git מותקן (`git --version`).
3. לחלץ את ה־zip לתיקייה, למשל `toys-api`.

אסור להעלות: `.env` ו־`node_modules`. מותר להעלות `.env.example`.

### GitHub Desktop

1. File → Add local repository → תיקיית הפרויקט.
2. לוודא ש־`.env` ו־`node_modules` לא מופיעים בשינויים.
3. Commit: `Toys API — Node.js MongoDB JWT`
4. Publish repository כ־**Public** (לבטל Keep this code private).
5. להגיש: `https://github.com/YOUR_USERNAME/toys-nodejs-mongo-api`

### שורת פקודה

ליצור ריפו Public ריק באתר, בלי README אוטומטי, ואז:

```bash
cd toys-api
git init
git add .
git status
git commit -m "Toys REST API with JWT auth and bilingual docs"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/toys-nodejs-mongo-api.git
git push -u origin main
```

ב־`git status` אסור ש־`.env` יופיע. בדיקה:

```bash
git check-ignore .env
git check-ignore node_modules
```

אחרי ההעלאה לפתוח את הריפו בחלון פרטי: Public, יש קוד ו־`.env.example`, אין `.env` ואין `node_modules`.

## English

Submit from the student's own public GitHub account. This repo is a reference only.

Do not upload `.env` or `node_modules`. Upload `.env.example`.

```bash
cd toys-api
git init
git add .
git status
git commit -m "Toys REST API with JWT auth and bilingual docs"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/toys-nodejs-mongo-api.git
git push -u origin main
```

Create an empty public repo first, without a generated README.
