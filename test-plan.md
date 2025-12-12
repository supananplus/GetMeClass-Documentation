# GetMeClass — Zoom App Test Plan (for Zoom Reviewers)

**Important:** If you need test accounts or credentials from us, request them via the contact link in our release notes and we will provide secure, temporary credentials.

---

## Summary / Purpose

This test plan walks Zoom reviewers through every step needed to verify the GetMeClass Zoom integration. It covers app OAuth authorization, each OAuth scope requested, and all user-facing functionality: creating/reading/updating/deleting meetings, attendance capture, and automatic recording downloads.

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
