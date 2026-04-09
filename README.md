# 🧵 GarmentOS — Real Platform Setup Guide

## Tech Stack
- **Frontend**: React Native (Expo)
- **Backend**: Firebase (Auth + Firestore)
- **APK**: EAS Build
- **Auth**: Mobile + Password

---

## 📁 File Structure

```
GarmentOS_Real/
├── App.js
├── app.json
├── eas.json
├── package.json
├── firestore.rules               ← Firebase security rules
└── src/
    ├── config/
    │   └── firebase.js           ← 🔴 Fill your Firebase credentials here
    ├── context/
    │   └── AppContext.js         ← Auth state + real-time data
    ├── navigation/
    │   └── AppNavigator.js       ← Stack + Bottom tabs
    ├── screens/
    │   ├── LoginScreen.js        ← Login + Admin Register
    │   ├── DashboardScreen.js    ← Real-time dashboard
    │   └── WorkEntryScreen.js    ← Full work entry (Firestore)
    ├── services/
    │   ├── authService.js        ← Firebase Auth
    │   └── firestoreService.js   ← All Firestore CRUD + listeners
    └── utils/
        ├── theme.js              ← Colors + helpers
        └── validation.js         ← 7 business rules
```

---

## 🔥 Step 1: Firebase Setup (10 min)

### 1.1 Create Project
1. Go to → https://console.firebase.google.com
2. Click **"Add project"** → Name: `GarmentOS`
3. Disable Google Analytics → **Create project**

### 1.2 Enable Authentication
1. Left sidebar → **Authentication** → **Get started**
2. **Sign-in method** tab → Enable **Email/Password**
3. Save

### 1.3 Create Firestore Database
1. Left sidebar → **Firestore Database** → **Create database**
2. Select **Production mode** → Choose region (asia-south1 for India)
3. **Done**

### 1.4 Paste Security Rules
1. Firestore → **Rules** tab
2. Copy everything from `firestore.rules` → Paste → **Publish**

### 1.5 Get Config
1. **Project Settings** (gear icon) → **General** tab
2. Scroll down → **Your apps** → **Add app** → Web (`</>`)
3. App nickname: `GarmentOS Web` → **Register**
4. Copy the `firebaseConfig` object

### 1.6 Paste in App
Open `src/config/firebase.js` and replace:
```js
const firebaseConfig = {
  apiKey:            "AIzaSy...",        // ← paste here
  authDomain:        "garmentos-xxx.firebaseapp.com",
  projectId:         "garmentos-xxx",
  storageBucket:     "garmentos-xxx.appspot.com",
  messagingSenderId: "1234567890",
  appId:             "1:1234:web:abc",
};
```

---

## 📱 Step 2: Install & Run

```bash
# Clone/copy project
cd GarmentOS_Real

# Install dependencies
npm install

# Start Expo
npx expo start

# Scan QR with Expo Go app (Android/iOS)
# OR press 'a' for Android emulator
```

---

## 🏭 Step 3: First-Time Factory Setup

1. Open app → **"Admin Register"** tab
2. Fill:
   - Factory Name: `Sharma Garments Pvt Ltd`
   - Admin Name: `Raj Sharma`
   - Mobile: `9876543210`
   - Password: `admin123`
3. Register → App auto-creates departments + operations

### Add Supervisor
Currently via Firebase Console:
1. Auth → Add user → `9811111111@garmentos.app` / `sup123`
2. Firestore → `users` → Add doc with:
```json
{
  "uid": "<user_uid>",
  "name": "Anita Sharma",
  "mobile": "9811111111",
  "role": "supervisor",
  "tenantId": "<your_tenant_id>",
  "department": "Stitching",
  "staffId": "SUP001",
  "status": "active"
}
```
*(Will add in-app supervisor management in next version)*

---

## 📦 Step 4: Build Android APK

### 4.1 Install EAS CLI
```bash
npm install -g eas-cli
eas login   # Create account at expo.dev
```

### 4.2 Configure Project
```bash
eas build:configure
# This creates/updates eas.json
```

### 4.3 Update app.json
```json
"extra": {
  "eas": {
    "projectId": "YOUR_PROJECT_ID"  ← from expo.dev
  }
}
```

### 4.4 Build APK (free, ~10 min)
```bash
# Preview APK (free tier, direct install)
eas build --platform android --profile preview

# Production AAB (for Play Store)
eas build --platform android --profile production
```

### 4.5 Download
- EAS will give you a download link
- Install APK on Android: `adb install garmentos.apk`
- Or share download link directly

---

## 🗃️ Firestore Data Structure

```
/users/{uid}
  name, mobile, role, tenantId, tenantName, status

/tenants/{tenantId}
  name, adminUid, plan, createdAt

/tenants/{tenantId}/workers/{workerId}
  name, staffId, mobile, department, status

/tenants/{tenantId}/styles/{styleId}
  styleName, styleNumber, buyer, totalQuantity, completedQty, status

/tenants/{tenantId}/operations/{opId}
  name, nameHindi, pieceRate, department

/tenants/{tenantId}/workEntries/{entryId}
  date, workerId, workerName, styleId, operationId,
  pieces, pieceRate, amount, department, status

/tenants/{tenantId}/payments/{paymentId}
  workerName, month, totalPieces, grossAmount, netAmount, status
```

---

## 🔒 Auth Pattern

Mobile number `9876543210` → stored as `9876543210@garmentos.app` in Firebase Auth.
Users never see the email — they only enter mobile + password.

---

## ✅ What's Working

| Feature | Status |
|---------|--------|
| Mobile + Password Login | ✅ |
| Admin Registration | ✅ |
| Auto tenant + data seed | ✅ |
| Stay logged in (AsyncStorage) | ✅ |
| Real-time Dashboard | ✅ |
| Real-time Work Entries | ✅ |
| Add / Edit / Delete Entry | ✅ |
| All 7 validation rules | ✅ |
| Role-based access | ✅ |
| Bottom Tab Navigation | ✅ |
| APK build ready | ✅ |

## 🔜 Next to Add

- [ ] In-app Supervisor management
- [ ] Workers CRUD screen
- [ ] Styles CRUD screen
- [ ] Payments generation
- [ ] Attendance marking
- [ ] Export to Excel (XLSX)
- [ ] Push notifications (FCM)
