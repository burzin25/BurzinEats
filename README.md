# BurzinEats — deployment guide

This turns the app into a real web app: Google sign-in, your data synced to a
free Google Cloud (Firestore) database, reachable from any device at one URL.

Total cost: **$0** on normal personal usage (Firebase's free "Spark" plan).

---

## 1. Create the Firebase project (this *is* Google Cloud)

1. Go to **console.firebase.google.com** → **Add project**.
2. Name it anything (e.g. `burzineats`). Skip Google Analytics (not needed).
3. Once created, click the **`</>` (web) icon** on the project overview page to
   register a web app. Give it any nickname → **Register app**.
4. Firebase shows a `firebaseConfig` object like this:
   ```js
   const firebaseConfig = {
     apiKey: "AIza...",
     authDomain: "burzineats-xxxx.firebaseapp.com",
     projectId: "burzineats-xxxx",
     storageBucket: "burzineats-xxxx.appspot.com",
     messagingSenderId: "123456789",
     appId: "1:123456789:web:abcdef"
   };
   ```
   Copy these six values.

## 2. Paste the config into `index.html`

Open `index.html`, find this block near the top of the `<script type="module">`
section, and replace the placeholder strings with your six real values:

```js
const FIREBASE_CONFIG = {
  apiKey: "PASTE_YOUR_API_KEY",
  authDomain: "PASTE_YOUR_PROJECT_ID.firebaseapp.com",
  projectId: "PASTE_YOUR_PROJECT_ID",
  storageBucket: "PASTE_YOUR_PROJECT_ID.appspot.com",
  messagingSenderId: "PASTE_YOUR_SENDER_ID",
  appId: "PASTE_YOUR_APP_ID"
};
```

## 3. Turn on Google sign-in

In the Firebase console: **Build → Authentication → Get started → Sign-in method**
→ enable **Google** → pick a support email → **Save**.

## 4. Create the Firestore database

**Build → Firestore Database → Create database** → pick any region close to you
→ start in **production mode**.

Then go to the **Rules** tab and replace the default rules with the contents of
`firestore.rules` (included in this download) → **Publish**. This makes sure
each Google account can only ever read or write its own data.

## 5. Get a free Gemini API key

Go to **aistudio.google.com** → **Get API key** → **Create API key**. Free, no
credit card. You'll paste this *inside the app itself* later (Profile → AI
settings) — it's stored in your Firestore data, not in this file.

## 6. Put the code on GitHub

1. Create a new GitHub repo (public or private both work).
2. Upload `index.html` (with your Firebase config already pasted in) to the
   repo root.
3. Go to the repo's **Settings → Pages**.
4. Under **Source**, choose **Deploy from a branch**, branch `main`, folder `/ (root)` → **Save**.
5. GitHub gives you a URL after a minute or two, shaped like:
   `https://YOUR-USERNAME.github.io/YOUR-REPO-NAME/`
   That's your app's permanent URL, usable on any device.

## 7. Authorize that URL with Firebase

Back in Firebase: **Authentication → Settings → Authorized domains → Add domain**
→ add `YOUR-USERNAME.github.io` (no `https://`, no trailing path). Without this
step, Google sign-in will fail with an "unauthorized domain" error.

## 8. Use it

Open your GitHub Pages URL on your phone, laptop, whatever. Sign in with
Google. Fill in your profile, then go to **Profile → AI settings** and paste
your Gemini API key. Everything you log from then on syncs automatically —
open the same URL on a different device and sign in with the same Google
account to see the same data.

---

### Notes and honest limitations

- **Free tier limits**: Firestore's free plan (Spark) gives 50,000 reads and
  20,000 writes per day — enormous headroom for one person's food logging.
  Gemini's free tier has its own daily rate limits; if you hit them, AI
  features will briefly fail with an error, not crash the app.
- **The Gemini key lives in the browser.** There's no backend server hiding
  it — it's sent directly from your device to Google each time it's used.
  Fine for a personal key on a personal app; don't reuse a key you share
  elsewhere.
- **The old free built-in Claude access does not work here.** That only
  exists inside Claude.ai's own interface. Outside of it, your Gemini key is
  what powers food parsing, suggestions, and report reading.
- **Camera barcode scanning and voice logging** depend on the browser
  granting camera/microphone permission to your GitHub Pages site — most
  browsers will prompt for this the first time, same as any web app.
