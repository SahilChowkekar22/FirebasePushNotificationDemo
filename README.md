
# 🔥 FirebasePushMessaging

An iOS SwiftUI app that demonstrates how to integrate **Firebase Cloud Messaging (FCM)** for push notifications.  
This guide walks through all the steps taken to configure Firebase, connect it with your Xcode project, and handle push notifications in a SwiftUI-based iOS app.

---

## 🚀 Features

- ✅ Firebase Integration via Swift Package Manager  
- 🔔 Push Notification setup using Firebase Cloud Messaging (FCM)  
- 📱 Native APNs configuration and token registration  
- 🔒 Proper entitlements and Info.plist configuration  
- 🔁 Real device support (Push only works on physical devices)

---

## 📦 Tools & Stack

- 🔧 **Swift 5**, **SwiftUI**  
- ☁️ **Firebase** (Messaging, Core)  
- 📲 **Apple Push Notification service (APNs)**  
- 🧰 **Xcode 14+**, tested on iOS 15+

---

## 🔗 Step-by-Step Firebase Setup Guide

### 1. 🎯 Create Firebase Project
- Go to [Firebase Console](https://console.firebase.google.com/)
- Click **Add project**
- Name it something like `FirebasePushMessaging`
- Disable Google Analytics (optional)
- Click **Create**

### 2. 📱 Add iOS App to Firebase
- Click **Add app** → Select iOS (Apple icon)
- Use your **iOS bundle ID** (e.g., `com.yourname.FirebasePushMessaging`)
- Download the `GoogleService-Info.plist` and **add it to your Xcode project**  
  ✅ This file is already in:  
  `FirebasePushMessaging/FirebasePushMessaging/GoogleService-Info.plist`

### 3. 🛠 Add Firebase SDK (Swift Package Manager)
- In Xcode: `File > Add Packages`
- Enter this URL:  
  ```
  https://github.com/firebase/firebase-ios-sdk
  ```
- Add these:
  - `FirebaseCore`
  - `FirebaseMessaging`

### 4. 🧠 Configure Firebase in App

Inside `FirebasePushMessagingApp.swift`:

```swift
import SwiftUI
import Firebase

@main
struct FirebasePushMessagingApp: App {
    init() {
        FirebaseApp.configure()
    }

    var body: some Scene {
        WindowGroup {
            ContentView()
        }
    }
}
```

### 5. 📡 Register for Notifications

Create a notification manager:

```swift
import UserNotifications
import FirebaseMessaging

class NotificationManager: NSObject, UNUserNotificationCenterDelegate, MessagingDelegate {
    func requestAuthorization() {
        UNUserNotificationCenter.current().delegate = self
        UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .sound, .badge]) { granted, error in
            if granted {
                DispatchQueue.main.async {
                    UIApplication.shared.registerForRemoteNotifications()
                }
            }
        }
        Messaging.messaging().delegate = self
    }

    func messaging(_ messaging: Messaging, didReceiveRegistrationToken fcmToken: String?) {
        print("FCM Token: \\(fcmToken ?? "")")
    }
}
```

Call `NotificationManager().requestAuthorization()` early (e.g., inside `App.init()` or your ContentView `onAppear`).

---

## 🔐 Permissions & Entitlements

### 🔸 Entitlements
In `FirebasePushMessaging.entitlements`:

```xml
<key>aps-environment</key>
<string>development</string>
```

Switch to `production` before App Store release.

### 🔸 Info.plist

```xml
<key>FirebaseAppDelegateProxyEnabled</key>
<false/>
<key>UIBackgroundModes</key>
<array>
    <string>remote-notification</string>
</array>
```

---

## ✅ Testing Push Notifications

> ⚠️ Push Notifications **only work on a real device**

To test:
1. Run app on iPhone.
2. Copy FCM token from console logs.
3. Use Firebase Console or Postman to send test notification to that token.

---

## 🗂 Project Structure Overview

```
FirebasePushMessaging/
├── GoogleService-Info.plist                  # Firebase Config
├── FirebasePushMessagingApp.swift            # App entry point, Firebase init
├── FirebasePushMessaging.entitlements        # Push config
├── Info.plist                                # Permissions
├── ContentView.swift                         # Main UI
└── NotificationManager.swift (optional)      # UN delegate + token handling
```

---

##  Pro Tips

- Enable Push Notifications in Xcode → Signing & Capabilities  
- Use `FirebaseAppDelegateProxyEnabled = false` only if you're handling your own notification delegates  
- Use actual iOS devices to receive push notifications — simulators won’t work  
- Add your APNs authentication key to Firebase Project settings

---


