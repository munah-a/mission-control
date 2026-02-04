# Mission Control - Firebase Edition

A real-time task management dashboard using Firebase for authentication and data storage.

## Features

- 🔐 **Google Sign-In** - Secure authentication with Google accounts
- ☁️ **Real-time Sync** - Changes sync instantly across devices via Firestore
- 📱 **Offline Support** - Works offline with local persistence
- 🎯 **Kanban Board** - Drag & drop tasks between columns
- ✅ **Subtasks** - Break down tasks into smaller steps
- 💬 **Comments** - Add notes and feedback to tasks
- 🏷️ **Tags & Projects** - Organize with custom tags and projects
- 🔍 **Search** - Find tasks quickly

## Setup Instructions

### 1. Create a Firebase Project

1. Go to [Firebase Console](https://console.firebase.google.com)
2. Click **"Create a project"** (or select an existing one)
3. Follow the setup wizard (you can disable Google Analytics)

### 2. Add a Web App

1. In your Firebase project, click the **gear icon** → **Project settings**
2. Scroll down to **"Your apps"** and click the web icon (`</>`)
3. Register your app with a nickname (e.g., "Mission Control")
4. **Copy the `firebaseConfig` object** - you'll need this!

### 3. Enable Google Authentication

1. In Firebase Console, go to **Authentication** → **Sign-in method**
2. Click on **Google** provider
3. Toggle **Enable**
4. Add your email as support email
5. Click **Save**

### 4. Create Firestore Database

1. In Firebase Console, go to **Firestore Database**
2. Click **"Create database"**
3. Choose **"Start in production mode"** (we'll set rules next)
4. Select a location close to your users
5. Click **Enable**

### 5. Set Firestore Security Rules

Go to **Firestore Database** → **Rules** and paste:

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // Users can only access their own data
    match /users/{userId}/{document=**} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

Click **Publish**.

### 6. Configure the App

Open `index-firebase.html` and find the `firebaseConfig` object near the top of the `<script>` section:

```javascript
const firebaseConfig = {
    apiKey: "YOUR_API_KEY",
    authDomain: "YOUR_PROJECT.firebaseapp.com",
    projectId: "YOUR_PROJECT_ID",
    storageBucket: "YOUR_PROJECT.appspot.com",
    messagingSenderId: "YOUR_SENDER_ID",
    appId: "YOUR_APP_ID"
};
```

Replace these values with your actual Firebase config from Step 2.

### 7. Deploy to GitHub Pages

1. Create a new GitHub repository (or use existing)
2. Push the `index-firebase.html` file
3. Go to **Settings** → **Pages**
4. Set source to **main** branch
5. Your app will be live at `https://USERNAME.github.io/REPO/index-firebase.html`

### 8. Add Authorized Domain

1. In Firebase Console, go to **Authentication** → **Settings**
2. Click **Authorized domains**
3. Add your GitHub Pages domain: `USERNAME.github.io`

## Data Structure

```
users/
  {userId}/
    tasks/
      {taskId}/
        title: string
        description: string
        status: "permanent" | "backlog" | "in_progress" | "review" | "done"
        priority: "low" | "medium" | "high"
        project: string
        tags: string[]
        subtasks: [{title: string, done: boolean}]
        comments: [{author: string, text: string, timestamp: number}]
        createdAt: number
        updatedAt: number
        completedAt: number (optional)
    
    projects/
      {projectId}/
        id: string
        name: string
        icon: string (emoji)
        color: string (hex)
    
    activities/
      {activityId}/
        type: "created" | "moved" | "completed" | "deleted"
        task: string
        actor: string
        to: string (for moved)
        timestamp: number
```

## Troubleshooting

### "Firebase not configured" warning
Make sure you've replaced all the placeholder values in `firebaseConfig` with your actual Firebase project values.

### Sign-in popup blocked
- Make sure popups are allowed for your domain
- Try using an incognito/private window to rule out extension conflicts

### "Permission denied" errors
- Check that Firestore security rules are published
- Make sure you're signed in
- Verify the user ID matches in the database path

### Data not syncing
- Check browser console for errors
- Verify Firestore is enabled in your Firebase project
- Check that your domain is authorized in Firebase Authentication settings

## Local Development

You can run this locally by:
1. Open `index-firebase.html` directly in a browser
2. Or use a local server: `npx serve .`

Note: Google Sign-In may require `localhost` to be added to authorized domains in Firebase Console.

## Migration from Original Version

If you have existing data in the JSON-based version:

1. Sign in to the new Firebase version
2. Manually recreate your projects
3. Manually recreate your tasks

For automated migration, you would need to:
1. Export your `data/tasks.json`
2. Write a script to import into Firestore

## Differences from Original

| Feature | Original | Firebase Edition |
|---------|----------|------------------|
| Authentication | GitHub PAT | Google Sign-In |
| Data Storage | GitHub repo (JSON file) | Firestore |
| Sync | Manual commits | Real-time |
| Offline | No | Yes (with persistence) |
| Multi-device | Manual refresh | Automatic |
| Cron jobs | Yes | Not included |
| Templates | Yes | Not included |
| Archive | Yes | Not included |

The Firebase edition is a streamlined version focused on core task management. Advanced features like cron jobs, templates, and the archive system can be added later.
