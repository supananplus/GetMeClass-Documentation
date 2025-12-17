# GetMeClass — Zoom App Test Plan (for Zoom Reviewers)

## 1. Purpose of this Test Plan
This document provides Zoom reviewers with a **step-by-step** to verify the Get Me Class Zoom integration.
It covers: 
- App authorisation model
- OAuth scopes usage
- Meeting creation
- Student participation
- Attendance tracking via webhooks
- Cloud recording handling
- Deauthorisation behaviour
>  **Important**  
> Get Me Class uses a **single Zoom account owned and managed by Get Me Class.**  
> End users **do not authorise Zoom** and there is **no Zoom integration UI** for tutors or students.

---

## 2. Test Environment Requirements
**Required Accounts**
- **Zoom account** used by Get Me Class (already configred by our team)
- **Get Me Class Tutor account**
- **Get Me Class Student account**

**Environment**
- OAuth authorisation uses the **Production Client ID**
- Webhook endpoint is publicly accessible
- Cloud recording enabled on the Get Me Class Zoom account
  
---

## 3. OAuth & Authorisation Model
- OAuth authorisation is performed **once** by Get Me Class adminitrators.
- OAuth tokens are stored securely on the backend.
- Tutors and students:
  - Do **not** connect Zoom
  - Do **not** authorise OAuth
  - Do **not** log in with Zoom
- Zoom API calls are triggerd only by backend services.
This app follows a **server-managed Zoom integration model.**

---

## 4. OAuth Scopes & Feature Mapping

| Zoom Scope | Used for |
|-----|------|
| `/meeting:write` | Create Zoom meetings |
| `/meeting:read`  | Retrieve meeting details |
| `/recording:read` | Retrieve recording metadata |
| Webhooks | Attendance & recording events |

---

## 5. End-to-End Test Scenarios

### Scenario 1 : Create a Zoom Meeting
**Scopes Used: `/meeting:write`**
**Steps**
1. Log in to Get Me Class as a **Tutor**.
2. Navigate to Create Course (if not already created).
3. Select **Zoom Course**.
4. Enter course details.
5. Save the course.
6. Go to **Zoom Courses**.
7. Toggle **สถานะห้อง Zoom (Zoom Room Status)**.
8. Click **สร้างห้อง Zoom (Create Zoom Room)**.
**Expected Result**
- Backend calls `POST /users/me/meetings`
- Zoom meeting is created under the Get Me Class Zoom account
- Meeting details appear in Get Me Class UI

---

### Scenario 2: Student Joins Zoom Meeting 
**Scopes UseD: `/meeting:read`**
**Steps**
1. Log in as **Student** in Get Me Class
2. Open the Zoom Course.
3. Click **Go to Class**
4. Join the Zoom meeting using any valid Zoom account.
**Expected Result**
- Student joins successfully
- Zoom participant list shows the student
- No Zoom authorisation prompt appears

---

### Scenario 3: Attendance Tracking via Webhooks
**Mechanism:** Zoom Webhooks (`meeting.ended`, `participant.joined`, `participant.left`)
**Steps**
1. End the Zoom meeting.
2. Zoom sends webhook events to Get Me Class.
3. Backend processes participant data.
**Expected Result**
- Attendance record is created automatically
- Tutor can review attendance in Get Me Class
- No Manual attendance action required

---

### Scenario 4: Recording Processing & Download
**Scopes Used:** `/recording:read`
**Steps**
1. Ensure cloud recording is enabled.
2. End the meeting.
3. Zoom sends recording.completed webhook.
4. Backend retrieves recording metadata.
5. Recording is downloaded to Get Me Class storage on schedule.
**Expected Result**
- Recording status updated in database
- Recording file available to tutor
- Tutor does not need to access Zoom manually

---

## 6. Negative/Edge Case Testing
**Deauthorisaton**
1. Remove Get Me Class app from Zoom Marketplace.
2. Attempt to create a new Zoom meeting in Get Me Class.
**Expected**
- API calls fail gracefully
- User sees a clear error message
- No Zoom data is accessed

---

**Invalid Token**
- OAuth token revoked or expired
**Expected**
- Backend receives 401/403
- Error logged
- Zoom features disabled until reauthorisation

---

## 7. Deauthorisation & Data Handling
When the app is removed from Zoom:
- OAuth tokens are immediately deleted
- Zoom meeting IDs and identifiers are removed
- Zoom features stop functioning
- Existing Zoom meetings remain in Zoom
- Non-Zoom GetMeClass data remains unaffected

---

##  8. Logs & Evidence Available
We can provide:
- API call logs (redacted)
- Webhook delivery logs
- Screen recording demonstrating:
  - Course creation
  - Meeting creation
  - Student join
  - Attendance update
  - Recording availability

---

## 9. Reviewer Notes
- This app does not support user-managed Zoom connections.
- There is **no Zoom login or authorisation UI.**
- All Zoom functionality is handled by backend services.
- Student joining meetings with their own Zoom accounts does **not** imply OAuth authorisation

---









## Required test environment

- Use the **Production Client ID** during OAuth authorization.
- A tutor account and at least one student account in GetMeClass.
- Zoom account(s) for the tutor and student. (If reviewer prefers, we can provide temporary Zoom accounts for testing.)
- Ensure webhook endpoint and background worker are reachable from the internet (our staging/prod endpoints are public).

---

## Scopes requested (map to features)

> Replace this list with the exact scopes your app requests in the Zoom Marketplace if they differ.

- `/meeting:write` — Create and update meetings (Create Zoom Room for a course).
- `/meeting:read` — Read meeting details (view meeting info).
- `/recording:read` — Read cloud recording metadata and download links.

---

## Test Steps — End-to-End Functionality

> Before starting: ensure the tutor account has permission in GetMeClass to create courses.

### 1) Create a Zoom Course & Meeting (meeting:write)

1. Login to GetMeClass as **Tutor**.
2. Go to **Create Course** → select **Zoom** course type → fill course name, start & end date, price → Submit.
3. Navigate to **Zoom Courses** tab; locate the newly created course.
4. Toggle **สถานะห้อง Zoom** (Zoom Room Status) → On.
5. In the dialog, click **สร้างห้อง Zoom** (Create Zoom Room).
   - **Expected API calls (server logs):** `POST /users/me/meetings` with payload matching course schedule.
   - **Expected UX:** App redirect to Zoom application.

---

### 2) Student joins meeting (meeting:read)

1. Login in as Student (via browser or Zoom client) and join the meeting from เข้าห้องเรียน → course menu → Course with Zoom type → Go to class
2. The system redirects the student to join the Zoom meeting using a Zoom account with the same email as their GetMeClass account, ensuring correct participant → user ID mapping. This ensures proper participant-to-user ID mapping.
   - **Expected:**
     - Student can enter the meeting.
     - Zoom participant list displays both the Tutor and the Student.

**Checks:**

- The Zoom participant list shows the Student’s Zoom account email (matching the student’s GetMeClass email).
- GetMeClass can successfully retrieve meeting information via the meeting:read scope after the student joins.

---

### 3) Attendance Capture via Report/Webhook (report:read or webhook)

1. After the meeting ends, check that the webhook events were delivered (we subscribe to `meeting.ended`, `participant.joined`, `participant.left`).
   - **Expected webhook deliveries:** At minimum, `meeting.participant_left`, `meeting.ended`.
2. Backend should process participant events and generates an attendance record for the student.
   - **Expected:** In GetMeClass UI → Course → Manage Course → Attendance the student appears with the correct attendance status.

**Checks:**

- Participant data (ID/email) and timestamps from the webhook payloads match Zoom’s attendance data available via API.

---

### 4) Recording availability & auto-download (recording:read, recording:write)

1. Configure a meeting with cloud recording enabled (or use a recorded meeting).
2. After `recording.completed` event arrives, verify our system:
   - Marks recording as ready in DB.
   - Schedules or immediately downloads the recording to the tutor’s GetMeClass storage (per our policy: downloads occur every weekend).
3. Confirm file appears in Tutor’s storage location and is playable.

**Checks:**

- API call made to `/meetings/{meetingId}/recordings` (requires `/recording:read`) to obtain download URLs.
- Download uses valid `download_token` or access token and results in expected file size.
- Metadata saved in DB: filename, size, created_at.

---

## Edge / Negative tests

- Revoke app authorization in Zoom while GetMeClass tries to create a meeting → App should handle 401/403 and show a clear error message prompting reauthorization.
- Insufficient scopes: attempt an action without the corresponding scope (simulate by provisioning a token without /recording:read) and verify the app surfaces a correct error to the tutor and logs the error.
- Webhook signature mismatch: send a fake webhook with wrong signature → App should discard and log a security warning.

---

## Logs & Evidence to provide to Zoom Reviewers

Upon request we can supply:

- API logs showing the calls (e.g., POST /users/me/meetings, GET /meetings/{id}/recordings).
- Webhook delivery logs (headers, payloads, signature verification).
- Database snapshot (redacted) showing `zoom_meeting_id`, `recording_status`, and attendance records created for the test meeting.
- A short screen recording or Zoom Clip that demonstrates: app install → create course → create meeting → student join → meeting end → recording available.

## Data removal & deauthorization behavior

- When the app is removed in Zoom (deauthorization), we delete OAuth tokens and Zoom-specific identifiers for that account. Course and user data in GetMeClass (non-Zoom data) remain, but Zoom features are disabled until reauthorization.
- We will provide a deauth webhook log to show receipt of the deauthorization event if requested.

---

## Contact & how to get test credentials

If reviewers want ready-made test credentials, we can provide them securely. Please request them by replying to the App Review message or emailing our Developer Support contact. When we provide credentials we will:

- Create temporary GetMeClass tutor/student accounts with known emails and passwords.
- Provide Zoom accounts (if required) or instructions to use your own Zoom reviewer accounts.
- Note: for security we will share any real credentials via a secure link or via the Zoom review console private notes field.
