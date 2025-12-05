# Firebase Setup Guide for 3LANCER Watchlist

This guide explains how to set up Firebase Firestore to store email watchlist submissions.

## Step 1: Create a Firebase Project

1. Go to [Firebase Console](https://console.firebase.google.com/)
2. Click **"Add project"** or **"Create a project"**
3. Enter project name: `3lancer-watchlist` (or your preferred name)
4. Click **Create project** (disable Google Analytics if you don't need it)
5. Wait for the project to be created

## Step 2: Get Your Firebase Config

1. In the Firebase Console, click the **gear icon** ⚙️ → **Project Settings**
2. Scroll down to **Your apps** section
3. Click **`</>` (Web)** to add a web app
4. Enter app name: `3lancer-web`
5. Check **"Also set up Firebase Hosting for this app"** (optional)
6. Click **Register app**
7. Copy the Firebase config object that appears - it looks like:

```javascript
const firebaseConfig = {
  apiKey: "AIzaSyDxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
  authDomain: "your-project.firebaseapp.com",
  projectId: "your-project-id",
  storageBucket: "your-project-id.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abcdef123456",
};
```

## Step 3: Update index.html with Your Config

1. Open `index.html` in your editor
2. Find the Firebase configuration section (around line 15-27)
3. Replace the placeholder config with your actual Firebase config from Step 2
4. Save the file

## Step 4: Enable Firestore Database

1. In Firebase Console, go to **Build** → **Firestore Database**
2. Click **Create database**
3. Choose location (closest to your users)
4. Select **Start in test mode** (for development - secure it later)
5. Click **Create**

## Step 5: Set Firestore Security Rules (for production)

Once you're ready for production, update your security rules:

1. Go to **Firestore Database** → **Rules** tab
2. Replace the rules with:

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /watchlist/{document=**} {
      // Anyone can read
      allow read: if true;
      // Only allow writes from web origins
      allow create: if request.resource.data.email is string
                    && request.resource.data.email.matches('^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\\.[a-zA-Z]{2,}$');
      // Don't allow updates or deletes
      allow update, delete: if false;
    }
  }
}
```

3. Click **Publish**

## Step 6: Test Your Setup

1. Refresh your local website (`localhost:8000`)
2. Submit an email through the watchlist form
3. In Firebase Console, go to **Firestore Database** → **watchlist** collection
4. You should see your email entry with timestamp

## Step 7: View Submitted Emails

### In Firebase Console:

1. Go to **Firestore Database**
2. Click on **watchlist** collection
3. View all submitted emails

### Export Emails (CSV):

1. Install Firebase CLI: `npm install -g firebase-tools`
2. Run: `firebase firestore:export ./exports`
3. Emails will be in the generated files

### Send Emails to Subscribers (when launching):

Use a service like:

- **Firebase Extensions** - Email Trigger
- **Mailchimp** - Import contacts and send campaign
- **SendGrid** - API integration
- **Gmail** - Manual sending (small list only)

## Fallback: Local Storage

If Firebase isn't configured, emails automatically store in browser's `localStorage` as fallback. You can access them in browser console:

```javascript
JSON.parse(localStorage.getItem("watchlist"));
```

## Deployment

When deploying to production:

1. Update your Firebase security rules (see Step 5)
2. Deploy your website with the Firebase config in place
3. Test the form from the live URL
4. Firebase will validate and store emails automatically

## Cost Considerations

Firebase Firestore free tier includes:

- ✅ 1 GB storage
- ✅ 50,000 reads/day
- ✅ 20,000 writes/day
- ✅ 20,000 deletes/day

This is more than enough for a watchlist! You'll only pay if you exceed these limits.

## Support

For more info:

- [Firebase Documentation](https://firebase.google.com/docs)
- [Firestore Pricing](https://firebase.google.com/pricing)
- [Firebase Security Rules](https://firebase.google.com/docs/firestore/security/start)
