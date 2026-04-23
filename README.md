# HabitFlow — Full-Stack Habit Tracker

A production-grade habit tracker with Firebase backend, AI coaching, analytics, and a polished UI.

---

## 🏗 Architecture

```
src/
├── components/
│   ├── auth/          AuthPage (Google sign-in)
│   ├── coach/         AI Coach chat interface
│   ├── dashboard/     Dashboard, stats, habit rows, reminders
│   ├── habits/        Habit management + modal
│   ├── analytics/     Charts, per-habit views, heatmaps
│   └── shared/        AppShell, LoadingScreen, Charts
├── hooks/
│   ├── useAuth.js     Firebase auth listener
│   └── useHabits.js   Real-time data + CRUD + AI triggers
├── services/
│   ├── firebase.js    App init + exports
│   ├── habitsService.js  Firestore CRUD
│   ├── storageService.js Firebase Storage (icon uploads)
│   └── aiService.js   AI coach (calls secure backend)
├── store/
│   └── useStore.js    Zustand global state
└── utils/
    ├── analytics.js   Streaks, consistency, chart data
    └── icons.js       80+ categorized emoji icons
api/
└── coach.js           Vercel serverless function (GPT-4)
```

---

## 🔥 Firestore Schema

```
users/{uid}
  ├── displayName, email, photoURL, createdAt, updatedAt
  │
  ├── habits/{habitId}
  │     name, description, category, icon, iconUrl, iconStoragePath
  │     color, days[0-6], frequency, active
  │     reminderEnabled, reminderTime
  │     uid, createdAt, updatedAt
  │
  ├── completions/{completionId}
  │     habitId, dateKey (YYYY-MM-DD), uid, completedAt
  │
  └── coachMessages/{messageId}
        type, message, insight, suggestion
        userMessage?, relatedHabitId?
        createdAt
```

---

## ⚡ Quick Start

### 1. Clone and install

```bash
git clone <your-repo>
cd habitflow
npm install
```

### 2. Create Firebase project

1. Go to [console.firebase.google.com](https://console.firebase.google.com)
2. Create new project
3. Enable **Authentication** → Google provider
4. Enable **Firestore** (start in test mode, then apply rules)
5. Enable **Storage**

### 3. Configure environment

```bash
cp .env.example .env.local
```

Fill in your Firebase config from the Firebase console:

```env
VITE_FIREBASE_API_KEY=...
VITE_FIREBASE_AUTH_DOMAIN=...
VITE_FIREBASE_PROJECT_ID=...
VITE_FIREBASE_STORAGE_BUCKET=...
VITE_FIREBASE_MESSAGING_SENDER_ID=...
VITE_FIREBASE_APP_ID=...
```

### 4. Apply security rules

Copy contents of `firebase.rules` into:
- Firebase Console → Firestore → Rules
- Firebase Console → Storage → Rules (the section inside `/* */`)

### 5. Run locally

```bash
npm run dev
```

---

## 🤖 AI Coach Setup (GPT-4)

The AI coach uses a **secure backend route** — the OpenAI key is never exposed to the browser.

### Vercel deployment (recommended)

1. The file `api/coach.js` is your Vercel serverless function
2. Set environment variable in Vercel dashboard:
   ```
   OPENAI_API_KEY=sk-...
   ```
3. The frontend calls `/api/coach` automatically on Vercel

### Local development (AI bypass)

Without the API key, the coach uses graceful fallback messages automatically.  
No setup needed for local dev — just note AI responses will be templated fallbacks.

### Netlify alternative

Create `/.netlify/functions/coach.js` with the same logic from `api/coach.js`.

---

## 🚀 Deployment

### Vercel (recommended)

```bash
npm install -g vercel
vercel
```

Set all `VITE_FIREBASE_*` env vars in Vercel Dashboard → Settings → Environment Variables.

### Netlify

```bash
npm run build
# drag dist/ folder to Netlify dashboard
```

Or connect repo and set build command: `npm run build`, publish dir: `dist`

---

## 📱 Firebase Considerations

- **Firestore indexes**: Compound queries on `dateKey + habitId` may require index creation — Firebase will show a link in the console to create them automatically
- **Offline support**: Firestore has built-in offline persistence — enable with `enableIndexedDbPersistence(db)` if needed
- **Storage CORS**: If icon uploads fail from localhost, configure CORS in Firebase Storage settings
- **Auth domains**: Add your Vercel/Netlify domain to Firebase Console → Authentication → Authorized Domains

---

## 🔔 Notification Architecture

The reminder system is UI-ready. To activate push notifications:

1. Enable **Firebase Cloud Messaging** in the Firebase console
2. Add FCM service worker to `public/`
3. Request notification permission on login
4. Use Firebase Cloud Functions to send notifications based on habit reminder times

The `reminderEnabled` and `reminderTime` fields are already stored per habit.

---

## 🎨 Design System

- **Font**: DM Sans (body) + DM Mono (labels/code)
- **Colors**: Violet/purple primary, green for success, amber for streaks
- **Background**: Deep dark `#0d0618` with subtle purple ambient glow
- **Cards**: `rgba(255,255,255,0.03)` with `rgba(255,255,255,0.06)` border
- **Tailwind**: Extended config in `tailwind.config.js`

---

## 📦 Dependencies

| Package | Purpose |
|---|---|
| `firebase` | Auth, Firestore, Storage |
| `react-router-dom` | Client-side routing |
| `recharts` | Charts and analytics |
| `zustand` | Global state management |
| `date-fns` | Date utilities |
| `clsx` | Conditional classnames |
