# CSC 4360 - In-Class Activity 13: Firebase Authentication

**Name:** Adi Tauqir (Undergraduate)  
**Course:** CSC 4360 - Mobile Application Development  
**Repository:** https://github.com/aditauqir/inclass-13-auth  

---

## Overview & Setup

How to get started:

1. **Clone the repo and get packages:**
   ```bash
   git clone https://github.com/aditauqir/inclass-13-auth.git
   cd inclass-13-auth
   flutter pub get
   ```

2. **Required Firebase Configuration:**
   The app connects to Firebase project **inclass-13** (Project ID: `inclass-13-dfe78`). If connecting to your own Firebase project:
   - Enable **Email/Password** sign-in under *Authentication > Sign-in method*.
   - Enable **Cloud Firestore** and publish the user-scoped rule under *Firestore > Rules*:
     ```javascript
     rules_version = '2';
     service cloud.firestore {
       match /databases/{database}/documents {
         match /users/{uid} {
           allow read, write: if request.auth != null && request.auth.uid == uid;
         }
       }
     }
     ```
   - Place your `google-services.json` inside `android/app/`.

3. **Run the App:**
   Launch an Android emulator (minSdkVersion 23+) or connect a physical device, then run:
   ```bash
   flutter run
   ```

4. **Run Tests & Static Analysis:**
   ```bash
   flutter analyze
   flutter test
   ```

---

## Implemented Features

**Authentication Service (`lib/services/auth_service.dart`)**: A clean service class I wrote so the UI screens do not call `FirebaseAuth` directly. It handles registering new accounts, signing in, logging out, updating passwords, and recent login reauth, plus it passes down the `authStateChanges()` stream.

**Reactive Route Guard (`lib/screens/auth_gate.dart`)**: Sits at the root of the app using a `StreamBuilder` that listens to `authStateChanges()`. If someone is logged in, it shows `ProfileScreen`. If they log out, it switches back to `AuthenticationScreen` right away, preventing access via the Android back button.

**Authentication & Validation (`lib/screens/authentication_screen.dart`)**: Lets users switch between Sign In and Register with a single tap. I put form validators on the email and password fields so empty submissions get blocked with red error text right on the device without sending network requests to Firebase.

**Protected Profile & Password Management (`lib/screens/profile_screen.dart`)**: Shows the signed-in user's email and their Firebase UID. Users can enter a new password here to update it, and the input field clears itself right after. If Firebase returns a `requires-recent-login` exception because the session is old, an alert dialog prompts for their current password to re-authenticate.

**Data Warden Extension**: An optional Firestore feature on the profile screen that stores a persistent note under `/users/{uid}`. It uses a real-time stream to show the saved note, and Firestore security rules restrict read and write access strictly to the authenticated owner (`request.auth.uid == uid`).
