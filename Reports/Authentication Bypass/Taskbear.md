# Email Verification OTP Bypass During User Registration in TaskBear Sign-up Page

## Severity
Medium

## OWASP Top 10:2025
A07 – Authentication Failures

## Vulnerability Type
Email Verification OTP Bypass

## Summary

During testing of the TaskBear registration workflow, modifying the email OTP verification response in Burp Suite from `401 Unauthorized` to a previously captured `201 Created` response allowed the client-side registration process to proceed.

After completing the registration form, the application displayed an account creation success notification and loaded the user dashboard.

This behavior indicates that the registration workflow may rely on client-side response handling to determine whether verification succeeded. Further testing is required to establish whether the backend independently enforces email verification and whether an account can remain usable without verified email ownership.

## Affected Asset

- **Application:** TaskBear
- **Affected Page:** Sign-Up / Registration Page
- **Affected URL:** https://taskbear.com.au/auth/sign-up?redirect=%2F
- **Vulnerability:** Email Verification OTP Bypass

## Steps to Reproduce (PoC)

1. Navigate to the TaskBear sign-up page and enter the required registration details using an email address under your control.

2. Submit a valid email verification OTP and capture the request and server response using Burp Suite Proxy. Observe the successful `HTTP/2 201 Created` response.

3. Intercept an OTP verification request and replace the OTP value with a fabricated value (`123456`).

4. Forward the modified request and inspect the server response. The server returns `HTTP/2 401 Unauthorized` with the message `OTP invalid or expired`.

5. Modify the intercepted response in Burp Suite by replacing the `401 Unauthorized` status and error response body with the previously captured `201 Created` status and successful response body. Forward the modified response to the application.

   **Observed Result:** The application proceeded to the next registration step after receiving the modified success response, despite the original OTP verification request returning `401 Unauthorized`.

6. Complete the registration form with the required account details and submit it.

7. Observe the resulting application state. The application displays the user dashboard and an account creation success notification.


## Proof of Concept (PoC)

### Screenshot 1: Sign-Up Page

**Description:** The application's sign-up page displaying the registration form.

**Action Performed:** Entered a randomly generated email address in the email field to begin the registration process.

<img width="736" height="710" alt="image" src="https://github.com/user-attachments/assets/b4538357-5483-4f1f-8eda-4e0f3ae75e14" />

### Screenshot 2: Valid OTP Verification Response

**Description:** Captured the server's response to a valid email OTP verification request using Burp Suite Proxy.

**Action Performed:** Entered the actual OTP received at the registered email address, intercepted the verification request, and examined the HTTP response to establish the application's normal OTP verification behavior.

**Observed Result:** The server returned an `HTTP/2 201 Created` response with a JSON body containing the email contact and verification timestamp.

<img width="1917" height="1017" alt="image" src="https://github.com/user-attachments/assets/c274e9a4-112f-4228-8fe6-b1ab7ba7f99e" />

### Screenshot 3: OTP Verification Request with Invalid OTP

**Description:** The email OTP verification request captured using Burp Suite Proxy, showing the submitted email address and a fabricated OTP value.

**Action Performed:** Intercepted the OTP verification request and replaced the actual OTP with a fabricated value (`123456`) to test whether the server properly validates the submitted OTP.

**Purpose:** To assess whether server-side OTP validation can be bypassed by submitting an invalid verification code.

<img width="1917" height="1016" alt="image" src="https://github.com/user-attachments/assets/8c3473c4-298f-456c-baf0-3ec082e5ad56" />

### Screenshot 4: Server Response to Invalid OTP

**Description:** The server response captured in Burp Suite after submitting a fabricated OTP (`123456`) to the email verification endpoint.

**Action Performed:** Forwarded the intercepted OTP verification request containing the fabricated OTP and inspected the server's response.

**Observed Result:** The server returned `HTTP/2 401 Unauthorized` with the message `OTP invalid or expired`, indicating that the server rejected the invalid OTP.

**Security Observation:** The server-side OTP validation rejected the fabricated code in this test. This screenshot alone does not demonstrate a successful OTP bypass.

<img width="1917" height="1017" alt="image" src="https://github.com/user-attachments/assets/ac594719-0a7a-421a-9de4-02034846c160" />

### Screenshot 5: Manual Modification of OTP Verification Response

**Description:** Burp Suite response editor showing a manually modified HTTP response for an OTP verification request containing an invalid OTP.

**Action Performed:** Captured the legitimate response from a valid OTP verification request, which returned `HTTP/2 201 Created`. Then, during testing with a fabricated OTP (`123456`), manually replaced the `401 Unauthorized` response status and response body with the previously captured successful response.

**Observed Result:** The response displayed `HTTP/2 201 Created` with the previously captured JSON response body. However, this response was manually modified in Burp Suite and was not returned by the server for the invalid OTP request.

**Security Observation:** This demonstrates client-side response manipulation only. It does not establish a server-side OTP verification bypass unless the application actually accepts the invalid OTP and marks the email as verified.

**Conclusion:** Further testing is required to determine whether the backend independently validates the OTP and whether registration can be completed without successful verification.

<img width="1917" height="1016" alt="image" src="https://github.com/user-attachments/assets/f3c54456-6ee9-470e-86fa-6db6ffcd4c5d" />

### Screenshot 6: Registration Form After OTP Bypass Attempt

**Description:** The TaskBear account registration form populated with the required user details, including first name, last name, email address, mobile number, date of birth, and password.

**Action Performed:** Entered the required registration details after the OTP verification bypass attempt and prepared the form for account creation.

**Observed Result:** The registration form was populated and the "Create account" button was available. This screenshot documents the registration stage but does not independently confirm successful account creation or email verification.

<img width="683" height="897" alt="image" src="https://github.com/user-attachments/assets/f8ebb912-0845-4555-ad8e-29fc8535b102" />

### Screenshot 7: Successful Account Creation After OTP Bypass

**Description:** The TaskBear user dashboard displayed after submitting the registration form.

**Action Performed:** Completed the registration process after the OTP bypass attempt and observed the resulting application state.

**Observed Result:** The application displayed the user dashboard, a welcome notification, and the success message: "Account created. You can verify your mobile later."

**Security Impact:** This demonstrates successful account creation following the tested registration flow. The relationship between the OTP verification attempt and account creation should be confirmed to establish whether email OTP verification was actually bypassed.

**Evidence:** The screenshot shows the authenticated dashboard and the account creation success notification.

<img width="1777" height="892" alt="image" src="https://github.com/user-attachments/assets/933c73a5-df61-48a6-a240-52737b10a7de" />


## Impact

- **Client-Side Verification Bypass:** Manipulating the OTP verification response allowed the registration workflow to proceed to the next step.
- **Potential Unverified Account Creation:** The application subsequently displayed an account creation success notification and loaded the user dashboard.
- **Email Verification Integrity:** If the backend does not independently enforce email verification, users may be able to create or access accounts without verifying ownership of their email addresses.
- **Potential Abuse:** The weakness could undermine the application's email verification controls and allow registration using unverified email addresses.

**Impact Limitation:** The available evidence demonstrates client-side response manipulation and successful account creation. It does not yet establish whether the backend marked the email address as verified or whether existing accounts can be taken over.

## Remediation

- **Enforce Server-Side Verification:** Validate email verification status on the backend before activating an account or granting access to protected functionality.
- **Do Not Trust Client Responses:** Never rely on HTTP status codes or response bodies received by the client as proof that email verification succeeded.
- **Maintain Server-Side Verification State:** Store and validate the email verification status on the server. Only update it after successful OTP validation.
- **Protect Registration Endpoints:** Ensure account creation and activation endpoints independently enforce all required verification checks.
- **Bind OTPs to Registration Sessions:** Associate each OTP with the intended email address and registration session.
- **Implement OTP Expiration and Single Use:** Reject expired or previously used OTPs.
- **Apply Rate Limiting:** Limit OTP generation and verification attempts to reduce brute-force attacks.

### Expected Security Outcome

The registration workflow must not treat a manually modified client-side response as successful email verification. Account activation and access to protected features must depend on verification enforced by the backend.
