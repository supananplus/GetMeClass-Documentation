# GetMeClass-Documentation

## 1. Adding the App
Follow these steps to add Get Me Class App to your Zoom account:

1. Log in to your Zoom Account.
2. Go to the [Zoom App Marketplace](https://marketplace.zoom.us/).
3. Search for **"Get Me Class"** and click on it.
4. Click **Add** or **Install**.
5. Follow the authorisation prompts to grant the required permissions.
6. If your encounter issues, see our [Support Page](https://getmeclass.com/#contact).

---

## 2. Using the App

### Feature 1: Create a Zoom meeting.
**Use Case:**
Automatically create a Zoom meeting for your class inside Get Me Class.
**Prequisites:**
- The Get Me Class app must be installed and authorised.
- Your Zoom account must allow meeting creation.
- You must be a tutor or tutor member inside Get Me Class.
**Steps:**
1. Open Get Me Class.
2. Click **Create Course** and select the **Zoom** course type.
3. Enter the course details (name, price, start date, end date etc.)
4. After submitting, go to the **Zoom Courses** tab.
5. Find your course and toggle **สถานะห้อง Zoom**(Zoom room status).
6. When the dialog appears, click **สร้างห้อง Zoom**(Create Zoom Room).
7. The system will create the Zoom meeting and redirect you to the Zoom meeting details page.
**Result:**
A Zoom meeting is now linked to your course and ready for students.

###Feature 2: View Participants(Attendance Tracking).
**User Case:**
Automatically track student attendance after each Zoom session.
**Prequisites**
- A Zoom meeting must already be created.
- Students must join the meeting using a valid Zoom account.
**Steps:**
1. After the Zoom meeting ends, a webhook automatically checks and records attendance for all participants.
**Result:**
Tutors and students no longer need to mark attendance manually.

###Feature 3: Download record Zoom file.
**User Case:**
Automatically download Zoom cloud recordings after the meeting ends.
**Prequisites**
- A Zoom meeting must already be created.
- The tutor must have sufficient storage available in their Get Me Class account.
**Steps:**
1. After the Zoom meeting ends, a webhook updates the recording status in the database.
2. Recordings are automatically downloaded to the tutor’s Get Me Class storage every weekend.
**Result:**
Tutors do not need to manually download Zoom recording files.

---

## 3. Removing the App
To remove (deauthoise) Get Me Class from your Zoom account:
1. Log in to Zoom.
2. Go to App Marketplace => Manage -> Installed Apps.
3. Search for Get Me Class.
4. Click Remove.
5. Confirm the deauthorisation.
**What Happens After Deauthorization**
- You will no longer be able to create or manage Zoom meetings through Get Me Class.
- Linked Zoom meetings will not be deleted from your Zoom account.
- Zoom-related features such as attendance syncing and recording downloads will stop working.
- All stored OAuth tokens and Zoom-related identifiers will be permanently deleted from our system.

## 4. Troubleshooting Guide
**Installation Problems**
- Ensure that your Zoom admin allows Marketplace apps.
- Verify that your Zoom account type supports app installation.

**Meeting Creation Errors**
- Confirm that your Zoom account has permission to create meetings.
- Check whether you have reached Zoom’s meeting creation limits.

**Redirect or Authorisation Issues**
- Disable popup blockers in your browser.
- Try an incognito window or a different browser.

Still stuck? Contact: [Get Me Class Support](https://getmeclass.com/#contact)

## 5. Privacy & Data Usage
- We store only the Zoom OAuth tokens, meeting IDs, and participant IDs required to perform app functions.
- We do **not** store your Zoom password.
- All tokens are encrypted and deleted immediately when you remove the app.
- We do not access or store Zoom chat logs.
Full privacy policy available on our website.

