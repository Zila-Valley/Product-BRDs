# Mobile App Documentation

## 1. Overview
The School ERP Mobile App is a cross-platform application built with Flutter. It serves as the primary portal for Parents, Students, and Teachers, offering real-time updates and seamless communication.

## 2. Technical Stack
- **Framework:** Flutter (Dart)
- **State Management:** Riverpod
- **Network calls:** Dio & Retrofit
- **Local Storage:** Isar (NoSQL database for caching)
- **Routing:** GoRouter (or Navigator 2.0 implementation)

## 3. Key User Flows

### 3.1 Parent/Student Portal
- **Dashboard:** Quick glance at today's attendance, upcoming exams, and pending fees.
- **Attendance Tracking:** Calendar view highlighting Present (Green), Absent (Red), and Holidays (Blue).
- **Homework & Assignments:** List of daily assignments. Allows downloading of attached PDFs/Images.
- **Fee Payments:** View detailed fee breakdowns and securely pay online via integrated payment gateways.
- **Notice Board:** Real-time push notifications for school circulars and announcements.
- **Report Cards:** View and download digitally signed term results.

### 3.2 Teacher Portal
- **Dashboard:** View assigned classes and today's timetable.
- **Mark Attendance:** Fast, touch-friendly UI to mark daily class attendance.
- **Homework Upload:** Take a photo or upload a PDF directly from the phone and assign it to a specific class.
- **Leave Application:** Apply for leaves and check approval status.

## 4. Architecture & State Management
The app uses a feature-first architecture (`lib/features/auth`, `lib/features/attendance`, etc.). 
- **Riverpod** is used for Dependency Injection and State Management.
- **Isar** database caches heavily accessed data (like Timetables and Notices) to ensure the app functions even with spotty internet connectivity.

## 5. Push Notifications
Push notifications are handled via Firebase Cloud Messaging (FCM). 
- **Registration:** Upon login, the app registers the device token with the backend API.
- **Routing:** Tapping a notification routes the user directly to the relevant screen (e.g., a "New Homework" notification opens the Homework screen).

## 6. Build & Deployment
- **Android:** Deployed as an AAB (Android App Bundle) via Google Play Console.
- **iOS:** Built via Xcode and deployed via TestFlight/App Store Connect.
- Environments (`.env.dev`, `.env.prod`) dictate which backend API URL the app points to during compilation.
