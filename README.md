# GetMeClass Documentation

## Overview
Get Me Class is a course management platform that integrates with Zoom to create online classes, track attendance, and manage recordings.
> **Important Architecture Note**
> Get Me Class uses **a single Zoom account owned and managed by Get Me Class.**
> End users (tutors and students) **do not connect, authorise, or log in with Zoom** inside the application.

---

## Zoom Account & Authoriastion Model
Get Me Class operates using a **server-managed Zoom integration model.**
- All Zoom API calls are executed using **one Zoom account and administered by Get Me Class.**
- Zoom OAuth authorisation is completed **once** by Get Me Class administrators.
- Tutors and students **do not authorise Zoom,** connect accounts, or grant permissions.
- There is **no "Connect Zoom or "Login with Zoom" page** in the UI by design.
- Students join meetings using standard Zom join links and their own Zoom accounts.
- OAuth tokens and Zoom identifiers are stored securely on the backend and are never exposed to end users.
This design ensures:
- Consistent meeting ownership
- Reliable webhook delivery
- Centralised recording access
- Simplified user experience

---

## 1. Adding the App
Follow these steps to install **Get Me Class** from the Zoom App Marketplace.
1. Log in to your Zoom account.
2. Go to the **[Zoom App Marketplace](https://marketplace.zoom.us/)**.
3. Search for **"Get Me Class"**.
4. Select the app and click **Add** or **Install**.
5. Review and approve the requested permissions.
6. Once installed, Get Me Class is authorised to access Zoom APIs using its managed Zoom account.
### Troubleshooting Installation Issues
- Ensure your Zoom account allows Marketplace apps.
- Confirm your account role permits app installation.
- If installation fails, visit our **[Support Page](https://getmeclass.com/#contact)**.

---

## 2. Using the App

### Feature 1: Create a Zoom Meeting

**Use Case:**  
Automatically create a Zoom meeting for a course managed in Get Me Class.

**Prerequisites:**  
- Get Me Class app is installed and authorised.
- You are a tutor or tutor member in Get Me Class.
- The Get Me Class managed Zoom account has meeting creation permissions.

**Steps:**  
1. Log in to Get Me Class.
2. Click **Create Course** and select **Zoom Course**.
3. Enter course details (All required fields).
4. Save the course.
5. Open the **Zoom Courses** tab.
6. Locate the course and toggle **สถานะห้อง Zoom (Zoom Room status).**
7. Click **สร้างห้อง Zoom (Create Zoom Room).**

**Result:**  
- A Zoom meeting is created using the Get Me Class managed Zoom account.
- Meeting details are displayed inside Get Me Class.
- Students can join from their Zoom course detail page.
  
---

### Feature 2: View Participants (Attendance Tracking)

**Use Case:**  
Automatically record student attendance after each Zoom session.

**Prerequisites:**  
- A Zoom meeting has been created for the course.
- Students join the meeting using a valid Zoom account.

**Steps:**  
1. Students join the Zoom meeting via the course link.
2. When the meeting ends, Zoom webhook events are delivered to Get Me Class.
3. Get Me Class processes participant join/leave events.

**Result:**  
- Attendance is recorded automatically.
- Tutors and students do not need to mark attendance manually.

**Note**
Attendance tracking uses Zoom webhook events. Students are **not required to authorise Zoom** with Get Me Class.

---

### Feature 3: Zoom Recording Management

**Use Case:**  
Automatically retrieve Zoom cloud recordings for completed sessions.

**Prerequisites:**  
- Cloud recording is enabled on the Get Me Class managed Zoom account.
- The tutor has sufficient storage in Get Me Class.

**Steps:**  
1. After a meeting ends, Zoo sends recording-related webhook events.
2. Get Me Class updates the recording status in system.
3. Recordings are automatically downloaded to the tutor's storage on a scheduled basis (every weekend).

**Result:**  
- Tutors do not need to manually download recordings from Zoom.
- Recordings are stored and accessible within Get Me Class.

---

## 3. Removing the App

To remove (deauthorise) **Get Me Class** from your Zoom:

1. Log in to Zoom account.  
2. Go to **App Marketplace → Manage → Installed Apps**.  
3. Search for **Get Me Class**.  
4. Click **Remove**.  
5. Confirm removal.

### What Happens After Deauthorisation

- Get Me Class can no longer create or manage Zoom meetings.
- Attendance tracking and recording downloads stop immediately.
- Existing Zoom meetings are not deleted from Zoom.
- All stored OAuth tokens and Zoom-related identifiers are permanently deleted from Get Me Class systems.
- Get Me Class data unrelated to Zoom remains intact.

---

## 4. Troubleshooting Guide

### Installation Issues
- Ensure Marketplace apps are enabled by your Zoom admin.
- Verify your Zoom account type supports app installation.

### Meeting Creation Issues
- Confirm the Get Me Class managed Zoom account has not reached meeting limits.
- Verify Zoom service availability.

### Recording Issues
- Ensure cloud recording is enabled on the Zoom account.
- Allow time for Zoom to finalise recording processing.

If problems persist, contact support: **[Get Me Class Support](https://getmeclass.com/#contact)**

---

## 5. Privacy & Data Usage

- Get Me Class stores only the data required for Zoom integration:
  - OAuth tokens
  - Zoom meeting IDs
  - Participant identifiers
- Zoom passwords are **never stored**.
- All tokens are encrypted at rest.
- Zoom tokens and identifiers are deleted immediately upon app removal.
- Zoom chat logs are not accessed or stored.
Full privacy policy is available on our website.

---

## Future Changes
If GetMeClass introduces:
- User-authorized Zoom connections
- Login with Zoom
- Multiple Zoom account support
We will submit a new app version for Zoom review with updated scopes, documentation, and test plans.
