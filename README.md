# NAYAK Agro Ledger — Setup Guide

**Login ID:** `Admin`  **Password:** `Nayak@2026`
(In Firebase this login is the user `admin@nayakagro.app`. The app adds `@nayakagro.app` for you, and the login ID is not case-sensitive.)

The Firebase config for project **nayak-agro** is already inside `index.html`.

---

## Step 1: Turn on login
1. Go to https://console.firebase.google.com and open project **nayak-agro**.
2. Open **Build → Authentication** → **Get started**.
3. Go to **Sign-in method** → **Email/Password** → turn on **Enable** → **Save**.
   Leave "Email link" off.

## Step 2: Create the Admin user
1. Go to **Authentication → Users → Add user**.
2. Email: `admin@nayakagro.app`
3. Password: `Nayak@2026`
4. Click **Add user**.

## Step 3: Create the database
1. Open **Build → Firestore Database** → **Create database**.
2. Location: **asia-south1 (Mumbai)**. This cannot be changed later.
3. Choose **Start in production mode** → **Create**.

## Step 4: Firestore rules
Go to **Firestore Database → Rules**, delete everything there, paste the rules below and click **Publish**:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {

    // Only the NAYAK Admin login can read or write business data
    match /biz/nayak/{col}/{doc} {
      allow read, write: if request.auth != null
                         && request.auth.token.email == 'admin@nayakagro.app';
    }

    // Everything else is blocked
    match /{document=**} {
      allow read, write: if false;
    }
  }
}
```

## Step 5: Allow your website domain
1. Go to **Authentication → Settings → Authorized domains → Add domain**.
2. Add your GitHub Pages domain, e.g. `yourname.github.io`.
   `localhost` and `nayak-agro.firebaseapp.com` are already allowed.

## Step 6: Put it online
1. Upload every file from the zip to a GitHub repository, e.g. `nayak-agro`.
2. Go to **Settings → Pages** → Branch: `main` / root → **Save**.
3. Open `https://yourname.github.io/nayak-agro/` and log in as **Admin / Nayak@2026**.
4. The top bar should show **● Online**.

## Step 7: Make the APK
1. Go to https://www.pwabuilder.com and paste your site link → **Package for stores → Android**.
2. Package ID: `com.nayak.agro`
3. Download the zip and install the `.apk` on your phones.
4. Keep the `signing.keystore` file from the zip safe. You need it to update the app later.

---

## Staying logged in and force logout
- **Stays logged in:** once you log in, the phone stays logged in, even after restarting the phone or closing the app.
- **Logout this phone:** More → Settings → *Logout this phone*. This logs out only that phone.
- **Force logout all devices:** More → Settings → *Force logout all devices*. This logs out **every phone at once**, including the one you press it on. Use it if a phone is lost or someone else knows the password.
- **Password changes:** change the password in Firebase → Authentication → Users → ⋮ → *Reset password*, or in the app under Settings → Change Password.
  - Phones that are already logged in **stay logged in** after a password change.
  - To lock out an old phone, first change the password, then press **Force logout all devices**. After that, only someone with the new password can log in.

## Where the data is stored
Firestore path `biz/nayak/`:
`machines`, `parties`, `txns`, `loans`, `loanPays`, `leases`, `bookings`, `config` (force-logout switch).
The app works offline and syncs automatically when the internet comes back.

## Backup
Go to More → Settings → **Download Backup** once a month and keep the JSON file safe, for example in Google Drive.
