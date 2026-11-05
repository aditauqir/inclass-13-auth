# CSC 4360 - In-Class Activity 13: Firebase Authentication

**Student:** Adi Tauqir  
**Course:** CSC 4360 - Mobile Application Development  
**Submission Due:** November 5, 2026 at 3:30 PM EST  
**Repository:** https://github.com/aditauqir/inclass-13-auth  
**Deliverables:** Release APK (`build/app/outputs/flutter-apk/app-release.apk`) and Evidence Document (`InClass13_AI_Auth_Lab_Adi_Tauqir.docx`)

---

## Overview & Setup

This is my Flutter app for In-Class Activity 13. Basically it handles Firebase email and password login, account registration, route guarding, and input validation so users cannot submit blank fields. I built it with Flutter and Dart for Android (minSdkVersion is set to 23 to meet Firebase library requirements), plus Cloud Firestore for the extra note saving part.

To get it running locally, just clone the repo and make sure Flutter and the Android SDK are installed on your machine. Run `flutter pub get` in the terminal to grab all the packages.

---

## Firebase Configuration

The application connects to my Firebase project (`inclass-13-dfe78`) through configuration generated using the FlutterFire CLI. Firebase is initialized asynchronously in `lib/main.dart` prior to running the app root widget using `Firebase.initializeApp(options: DefaultFirebaseOptions.currentPlatform)`.

Platform-specific metadata is managed through `android/app/google-services.json` and `lib/firebase_options.dart`. In adherence to security best practices, no sensitive API keys, user passwords, or backend service-account credentials are hardcoded or exposed in the repository.

---

## Implemented Features

- **Authentication Service (`lib/services/auth_service.dart`)**: A clean service class I wrote so the UI screens do not call `FirebaseAuth` directly. It handles registering new accounts, signing in, logging out, updating passwords, and recent login reauth, plus it passes down the `authStateChanges()` stream.
- **Reactive Route Guard (`lib/screens/auth_gate.dart`)**: Sits at the root of the app using a `StreamBuilder` that listens to `authStateChanges()`. If someone is logged in, it shows `ProfileScreen`. If they log out, it switches back to `AuthenticationScreen` right away, preventing access via the Android back button.
- **Authentication & Validation (`lib/screens/authentication_screen.dart`)**: Lets users switch between Sign In and Register with a single tap. I put form validators on the email and password fields so empty submissions get blocked with red error text right on the device without sending network requests to Firebase.
- **Protected Profile & Password Management (`lib/screens/profile_screen.dart`)**: Shows the signed-in user's email and their Firebase UID. Users can enter a new password here to update it, and the input field clears itself right after. If Firebase returns a `requires-recent-login` exception because the session is old, an alert dialog prompts for their current password to re-authenticate.
- **Data Warden Extension**: An optional Firestore feature on the profile screen that stores a persistent note under `/users/{uid}`. It uses a real-time stream to show the saved note, and Firestore security rules restrict read and write access strictly to the authenticated owner (`request.auth.uid == uid`).

---

## How to Run & Test

1. **Install Dependencies**:
   ```bash
   flutter pub get
   ```

2. **Run on Android Emulator or Device**:
   Launch a running Android emulator (API 23+) or connect a physical device, then run:
   ```bash
   flutter run
   ```

3. **Run Static Analysis & Automated Tests**:
   Verify code health and run automated test suites:
   ```bash
   flutter analyze
   flutter test
   ```

4. **Build Release APK**:
   To assemble the standalone release APK for submission:
   ```bash
   flutter build apk --release
   ```
   The compiled binary will be placed at `build/app/outputs/flutter-apk/app-release.apk`.
