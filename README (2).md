# Label Printer — Avery 5163 / 8163

A single-page web app that:
- Imports names from an Excel (.xlsx/.xls) or CSV file
- Lays them out for **Avery 5163 / 8163** label sheets (2" × 4", 10 labels per sheet)
- Prints perfectly aligned via your browser's normal Print dialog (Print → Save as PDF, or straight to your label printer)
- Optionally saves/loads your lists to the cloud (Firebase) so you can pull them up from your phone or another computer

No installs needed for the printing part — it's one HTML file. Firebase is only needed if you want cloud save.

---

## 1. Quick test (no setup)

Just open `index.html` in a browser (double-click it). Import a file and print. Cloud save will show "not configured" — that's fine, everything else still works.

## 2. Put it online with GitHub Pages (free)

1. Create a GitHub account if you don't have one: https://github.com/join
2. Create a new repository (e.g. `label-printer`), and upload `index.html` (and this README) to it.
   - On the repo page: **Add file → Upload files** → drag in `index.html` → **Commit changes**.
3. Go to the repo's **Settings → Pages**.
4. Under "Build and deployment", set **Source: Deploy from a branch**, **Branch: main**, folder `/ (root)`. Save.
5. GitHub gives you a URL like `https://YOUR_USERNAME.github.io/label-printer/` within a minute or two. That's your live app — bookmark it on your phone and laptop.

Any time you edit `index.html` in GitHub (or push a new version), the live site updates automatically.

## 3. Turn on cloud save with Firebase (optional, free tier is plenty)

This lets you save a name list once and load it from any device.

1. Go to https://console.firebase.google.com and create a new project (any name).
2. In the project, click **Build → Firestore Database → Create database**. Start in **test mode** (fine for personal use; you can lock it down later).
3. Click **Build → Authentication → Get started**, then enable the **Anonymous** sign-in provider (Sign-in method tab). This lets the app quietly log you in without a username/password.
4. Click the gear icon → **Project settings → General**, scroll to "Your apps", click the **</>** (web) icon, register an app (no hosting needed).
5. Firebase shows you a `firebaseConfig` object like:
   ```js
   const firebaseConfig = {
     apiKey: "AIza...",
     authDomain: "your-project.firebaseapp.com",
     projectId: "your-project",
     storageBucket: "your-project.appspot.com",
     messagingSenderId: "123456789",
     appId: "1:123456789:web:abcdef"
   };
   ```
6. Open `index.html`, find the `firebaseConfig` block near the top (search for `YOUR_API_KEY`), and paste your real values in.
7. Commit/upload the updated `index.html` to GitHub. Cloud save now works — the status line at the top right will say "Cloud save connected."

### Firestore security (recommended once it's working)
Test mode leaves your database open to anyone with the project ID. Once confirmed working, go to **Firestore → Rules** and use:
```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId}/lists/{listId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```
This restricts each anonymous account to only its own saved lists.

---

## 4. Using it

1. **Import**: drop in your Excel/CSV file.
2. **Pick the column** that holds the names (the app guesses if a column is literally named "Name").
3. Adjust font size / alignment if you like.
4. **(Optional)** Save the list to the cloud under a name, e.g. "Route 12 – August".
5. Click **Build label sheets**, then **Print / Save as PDF**.
6. In the print dialog: set paper size to **Letter**, margins to **None** or **Default** (the layout already includes correct margins), scale to **100%** (not "fit to page" — that would shift the label alignment).
7. Load the label sheets (Avery 5163 or 8163) into your printer and print.

## Notes
- The label grid is hard-coded to Avery 5163/8163 spec: 2" × 4" labels, 2 columns × 5 rows, 0.5" top margin, 0.15625" side margin. If your printer runs slightly off, most printers have a small margin offset setting — adjust there rather than in this file.
- Long names shrink to fit within the label box automatically (via smaller font) — if names get cut off, lower the "Font size" setting.
