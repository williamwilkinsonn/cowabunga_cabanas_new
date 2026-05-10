# Firestore Security Rules (Recommended)

```rules
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    function signedIn() { return request.auth != null; }
    function userDoc() { return get(/databases/$(database)/documents/users/$(request.auth.uid)); }
    function role() { return signedIn() ? userDoc().data.role : null; }
    function isManager() { return role() == 'manager'; }
    function isKitchen() { return role() == 'kitchen'; }
    function isFrontGate() { return role() == 'front_gate'; }
    function isGuestService() { return role() == 'guest_service'; }

    match /users/{uid} {
      allow read: if signedIn() && (request.auth.uid == uid || isManager());
      allow create: if signedIn() && (request.auth.uid == uid || isManager());
      allow update: if signedIn() && (request.auth.uid == uid || isManager());
      allow delete: if isManager();
    }

    match /sessions/{id} {
      allow read: if signedIn();
      allow write: if isManager() || isKitchen() || isFrontGate();
    }

    match /cabanas/{id} {
      allow read: if signedIn();
      allow write: if isManager() || isKitchen();
    }

    match /late_arrivals/{id} {
      allow read: if signedIn();
      allow write: if isManager() || isKitchen();
    }

    match /servers/{id} {
      allow read: if signedIn();
      allow write: if isManager();
    }

    match /settings/{id} {
      allow read: if signedIn();
      allow write: if isManager() || isKitchen();
    }

    match /weather/{id} {
      allow read: if signedIn();
      allow write: if isManager() || isKitchen();
    }

    match /alerts/{id} {
      allow read: if signedIn();
      allow write: if isManager() || isKitchen();
    }
  }
}
```

Notes:
- Manager can read/write all app data.
- Kitchen can update kitchen/session operational data only.
- Front Gate can update headcount/session data only.
- Guest Service is read-only for operational data.
- No unauthenticated access.
