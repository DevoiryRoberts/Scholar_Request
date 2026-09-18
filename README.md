<div align="center">
    
# ScholarRequest

**Full Stack Application** לניהול בקשות מענקים לסטודנטים — מהגשת בקשה ועד אישור מנהל.

![Node.js](https://img.shields.io/badge/Node.js-339933?style=for-the-badge&logo=nodedotjs&logoColor=white)
![React](https://img.shields.io/badge/React-20232A?style=for-the-badge&logo=react&logoColor=61DAFB)
![MongoDB](https://img.shields.io/badge/MongoDB-4EA94B?style=for-the-badge&logo=mongodb&logoColor=white)
![Redux](https://img.shields.io/badge/Redux-593D88?style=for-the-badge&logo=redux&logoColor=white)
![MUI](https://img.shields.io/badge/MUI-007FFF?style=for-the-badge&logo=mui&logoColor=white)
![JWT](https://img.shields.io/badge/JWT-000000?style=for-the-badge&logo=JSON%20web%20tokens&logoColor=white)

</div>
---

## Overview

מערכת Full Stack לניהול בקשות מענקים לסטודנטים. סטודנטים מגישים בקשה מפורטת בטופס רב-שלבי (פרטים אישיים, משפחה, לימודים, פרטי בנק), עוקבים אחר סטטוס הבקשה ומקבלים עדכון במייל; מנהל המערכת צופה בכל הבקשות הממתינות, מסנן אותן לפי קריטריונים שונים, ומאשר או דוחה כל בקשה.

## Key Features

**סטודנט**
- הרשמה וכניסה עם זיהוי לפי תעודת זהות
- טופס הגשה רב-שלבי עם ולידציה בכל שלב, כולל העלאת מסמכים תומכים
- שמירת טיוטה ידנית ואוטומטית (בסגירת הדפדפן, דרך `sendBeacon`)
- תצוגה מקדימה של הבקשה לפני שליחה סופית
- מעקב אחר סטטוס הבקשה האחרונה, עם עדכון אוטומטי במייל

**מנהל**
- טבלת בקשות ממתינות עם סינון ומיון בצד שרת (תעודת זהות, עיר, טווח תאריכים, מספר ילדים, שכר לימוד)
- צפייה בפרטי בקשה מלאים כולל מסמכים מצורפים
- אישור/דחייה של בקשה עם שליחת מייל אוטומטית לסטודנט

## Architecture

```
Client   → React + Redux Toolkit + MUI, תקשורת עם השרת דרך Axios ו-JWT ב-httpOnly Cookie
Server   → Express.js: Controllers, Routes, Middleware (Auth, Admin-only, Multer)
Services → Nodemailer לשליחת מיילים, Joi לולידציית קלט
Data     → MongoDB דרך Mongoose (אוספי Users ו-Requests)
```

## Getting Started

### Prerequisites
- Node.js 18+
- MongoDB (מקומי או Atlas)
- חשבון Gmail עם App Password לשליחת מיילים

### Installation

```bash
git clone <repository-url>
cd scholar-request

cd server && npm install
cd ../client && npm install
```

### Running locally

```bash
# Server (מתיקיית server)
npm run dev        # עם nodemon
npm start          # ללא nodemon

# Client (מתיקיית client, בטרמינל נפרד)
npm start
```

השרת רץ על `http://localhost:3002`, הלקוח רץ על `http://localhost:3000`.

## Environment Variables

יש ליצור קובץ `.env` בתיקיית `server`:

```env
MONGO_URI=mongodb://localhost:27017/scholarrequest
JWT_SECRET=your_super_secret_key_here
EMAIL_USER=your.email@gmail.com
EMAIL_PASS=xxxx xxxx xxxx xxxx
NODE_ENV=development
PORT=3002
```

> קובץ `.env` לא אמור להיות מועלה ל-Git — יש לוודא שהוא כלול ב-`.gitignore`.

ה-`EMAIL_PASS` נדרש להיות Gmail App Password (נוצר תחת Google Account → Security → 2-Step Verification → App Passwords), לא סיסמת החשבון הרגילה.

## API Reference

### Auth — `/api/auth`

| Method | Endpoint | תיאור | Auth |
|---|---|---|---|
| POST | `/register` | הרשמת משתמש חדש | – |
| POST | `/login` | כניסה למערכת | – |
| GET | `/check-auth` | בדיקת תוקף טוקן | נדרש |

### Requests — `/api/requests`

| Method | Endpoint | תיאור | Auth | Admin |
|---|---|---|---|---|
| POST | `/submit` | הגשת בקשה + קבצים | נדרש | – |
| POST | `/draft` | שמירת טיוטה + קבצים | נדרש | – |
| POST | `/draft-text` | שמירת טיוטה (טקסט בלבד) | נדרש | – |
| GET | `/my-draft` | שליפת הטיוטה הנוכחית | נדרש | – |
| GET | `/my-status` | סטטוס הבקשה האחרונה | נדרש | – |
| GET | `/pending` | כל הבקשות הממתינות + פילטרים | נדרש | כן |
| GET | `/get/:id` | בקשה ספציפית לפי ID | נדרש | כן |
| PUT | `/update/:id` | עדכון סטטוס (אישור/דחייה) | נדרש | כן |
| PUT | `/appeal/:id` | ערעור — החזרה להמתנה | נדרש | – |

פילטרים נתמכים ב-`/pending`: `id`, `city`, `fromDate`, `toDate`, `minSiblings`, `minSalary`, `maxSalary`, `sortBy`, `order`.

## Project Structure

```
scholar-request/
├── client/src/
│   ├── Components/
│   │   ├── Form/            # טופס הגשה רב-שלבי (Personal, Family, Studies, Bank, Verify)
│   │   ├── ViewRequest.jsx  # טבלת בקשות למנהל
│   │   ├── RequestDetails.jsx
│   │   └── Status.jsx       # דף סטטוס לסטודנט
│   ├── Redux/                # requestSlice, userSlice
│   └── api.js                 # Axios instance
│
└── server/
    ├── controllers/            # authController, requestController
    ├── models/                 # userSchema, requestSchema
    ├── routes/                 # authRoutes, requestRoutes (כולל Multer)
    ├── middleware/             # authMiddleware (JWT + adminOnly)
    ├── services/                # emailService (Nodemailer)
    ├── validations/             # Joi schemas
    ├── uploads/{userId}/draft|final/
    └── index.js                # נקודת כניסה לשרת
```

## Notable Implementation Details

- אימות מבוסס JWT הנשמר ב-httpOnly Cookie, כולל ניקוי בעת יציאה
- שמירת טיוטה ידנית ואוטומטית (`sendBeacon` בסגירת דפדפן)
- שדות דינמיים למספר אחים משתנה במשפחה
- הפרדת קבצים שהועלו לפי `draft/` ו-`final/` לכל משתמש

---

פרויקט גמר — קורס Full Stack React + Node.js
