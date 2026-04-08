# Deploy March Madness App: GitHub Pages + Firebase

## Context

The sales team needs remote access to the contest app. Players should be able to view live scores, shoot free throws from their own devices, and see results sync in real-time across all browsers. Deploying to GitHub Pages for hosting and Firebase Realtime Database as the single shared data store.

## Architecture

```
GitHub Pages (hosting)          Firebase Realtime DB (data)
├── index.html          ←→      ├── dailyScores/
                                ├── freeThrowLog/
                                ├── bracket/
Claude Code (scheduled task)    ├── overrides/
└── HubSpot MCP → Firebase      └── settings/
```

- **GitHub Pages**: Hosts `index.html` at a public URL
- **Firebase Realtime DB**: Stores ALL contest data (HubSpot scores, free throws, bracket, overrides)
- **Scheduled task**: Pulls HubSpot data via MCP → writes to Firebase (replaces data.json approach)
- **All browsers**: Listen to Firebase in real-time → instant updates everywhere

## Implementation Steps

### 1. User Creates Firebase Project

Manual steps (I'll walk you through):
1. Go to https://console.firebase.google.com
2. Create project "planhub-march-madness"
3. Create a Realtime Database (not Firestore) — choose "Start in test mode" for now
4. Go to Project Settings → General → scroll to "Your apps" → click Web (</>) → register app
5. Copy the `firebaseConfig` object (apiKey, databaseURL, etc.)

### 2. Update `index.html` — Add Firebase SDK + Real-Time Sync

Replace localStorage-based persistence with Firebase:

- Add Firebase SDK via CDN (`firebase-app.js`, `firebase-database.js`)
- Replace `save()` → writes to Firebase: `firebase.database().ref('/').set(D)`
- Replace `load()` → reads from Firebase on init + sets up real-time listener
- `firebase.database().ref('/').on('value', snapshot => { D = snapshot.val(); re-render all })`
- Free throw results instantly visible to all connected browsers
- Remove `fetchHubSpotData()` polling — Firebase listener replaces it
- Keep localStorage as offline fallback

### 3. Update Scheduled Task

Update `march-madness-hubspot-sync` to write to Firebase instead of `data.json`:
- Use Firebase REST API: `PUT https://{project}.firebaseio.com/dailyScores/{date}.json`
- No git push needed
- Still runs every 30 min via Claude Code, pulls HubSpot via MCP

### 4. Create GitHub Repo + Enable Pages

- `gh repo create planhub-march-madness --public --source=. --push`
- Enable GitHub Pages on main branch
- Team accesses app at `https://{username}.github.io/planhub-march-madness/`

### 5. Firebase Security Rules

Set basic rules to allow read/write (contest is short-lived, 9 days):
```json
{
  "rules": {
    ".read": true,
    ".write": true
  }
}
```

(Test mode does this by default for 30 days — perfect for our contest window)

## Critical Files

- `C:\Users\jrank_planhub\OneDrive - Planhub\Claude\March Madness\index.html` — Add Firebase SDK, replace localStorage with Firebase sync, keep localStorage as fallback
- Scheduled task `march-madness-hubspot-sync` — Update to write to Firebase REST API instead of data.json

## What Changes for Users

- **Players**: Open the GitHub Pages URL → see live scores, shoot free throws, results sync instantly
- **Admin**: Same admin panel, all changes propagate to everyone in real-time
- **Josh**: Scheduled task still runs from Claude Code, but writes to Firebase instead of a local file

## Verification

1. Firebase project created, config pasted into index.html
2. GitHub Pages URL loads the app
3. Open app in two browsers → shoot free throw in one → points appear in other within seconds
4. Admin sets bracket winner → all browsers update instantly
5. Scheduled task runs → HubSpot scores appear for all users
6. Toggle Madness Mode → all browsers show 2x indicator immediately
