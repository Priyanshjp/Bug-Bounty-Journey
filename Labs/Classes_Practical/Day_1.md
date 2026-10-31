# TaskBear OTP Verification

# Vulnerability Report

## Title

Potential Client-Side Trust in OTP Verification Response During Sign-Up — Server-Side Impact Unconfirmed

## Summary

During observation of the TaskBear sign-up flow, an invalid OTP submission returned an HTTP `401 Unauthorized` response. When the response was modified locally in Burp Suite to use `201 Created` with a modified response body, the browser interface appeared to proceed through the sign-up flow.

This observation may indicate that the client interface relies on response data to control the displayed verification state. However, server-side OTP verification bypass has not been established. Further validation would be required to determine whether the backend independently enforces OTP verification.

## Affected asset

- **Program:** TaskBear — authorization and program scope not yet confirmed
- **In-scope hostname / app:** `https://taskbear.com.au/` — subject to authorization and scope confirmation
- **Endpoint and HTTP method:** Sign-up and OTP verification flow; exact verification endpoint and HTTP method not recorded
- **Affected role:** Unauthenticated user attempting sign-up
- **Environment:** Web application; testing performed using Burp Suite

## Severity rationale

- **Suggested severity:** Informational / needs further validation; no confirmed vulnerability severity assigned
- **Impact:** The browser interface appeared to proceed after local HTTP response manipulation. No server-side verification bypass, account activation, or unauthorized access has been confirmed.
- **Exploitability / prerequisites:** The observed behaviour required interception and local modification of an HTTP response using Burp Suite. Whether an attacker can cause the server to accept an invalid OTP remains unknown.
- **Program-specific severity guidance considered:** Not assessed. The applicable vulnerability disclosure policy, scope, and severity guidelines must be reviewed before submitting the finding.

## Preconditions

- Access to the TaskBear sign-up page.
- An email address permitted for authorized testing.
- An OTP verification flow initiated through the application.
- Burp Suite configured to inspect the browser's HTTP traffic.
- Authorization to test the target under its applicable security testing policy.

## Steps to reproduce

1. Open the TaskBear sign-up page:  
   `https://taskbear.com.au/auth/sign-up?redirect=%2F`

2. Initiate the email verification process using an authorized test email address.

3. Enter an invalid OTP in the verification interface.

4. Intercept the relevant HTTP response in Burp Suite and record the original response.

5. Observe that the original response status is `401 Unauthorized`.

6. For the recorded observation, modify the response locally to use `201 Created` and change the response body.

7. Return the modified response to the browser and observe that the interface appeared to proceed through the sign-up flow.

8. Record the result as client-side behaviour only. Do not interpret the interface transition as proof of successful server-side OTP verification.

**Reproduction limitation:** The exact verification endpoint, HTTP method, original request, and full response have not been documented in this report. Server-side verification and account state changes remain unverified.

## Expected result

The application should reject an invalid or expired OTP and prevent verification or account activation.

The backend should independently validate the OTP and maintain the authoritative verification state. A locally modified HTTP response must not allow an account to become verified or grant access to protected functionality.

## Actual result

The original response was reported as `401 Unauthorized`. After the response was modified locally to use `201 Created` with a changed response body, the browser interface appeared to proceed.

No evidence currently establishes that the backend accepted the invalid OTP, activated the account, or granted access to protected resources.

## Security impact

The confirmed observation is limited to the browser interface responding differently after local HTTP response manipulation.

If an independently authorized test establishes that the backend accepts an invalid OTP or marks an unverified account as verified, this could represent an email verification bypass. Potential consequences would depend on the affected functionality and the application's reliance on verified email ownership.

**Confirmed impact:** Client-side interface behaviour changed following local response modification.

**Unconfirmed impact:** Server-side OTP bypass, account activation without verification, unauthorized access, and account takeover.

## Evidence

- **Video evidence:** `evidence/otp-bypass-demo.mp4`
- **Evidence description:** Demonstrates the OTP sign-up flow, the original `401 Unauthorized` response, and the locally modified response followed by the observed interface behaviour.
- **Evidence limitations:** The video should not be treated as proof of server-side verification bypass.

Before publishing or submitting evidence, redact email addresses, OTP values, cookies, bearer tokens, session identifiers, and other sensitive information.

## Remediation

- Enforce OTP validation on the server for every verification attempt.
- Update account verification state only after successful server-side OTP validation.
- Do not treat HTTP response status codes or client-controlled response bodies as proof of successful verification.
- Bind OTPs to the intended account and verification session.
- Enforce OTP expiration, attempt limits, and replay protection.
- Ensure protected backend endpoints independently check the account's verification status.
- Add server-side logging and monitoring for repeated failed verification attempts.

## Retest history

- **Date:** Not recorded
- **Result:** Initial observation documented; server-side impact unconfirmed
- **Notes:** No verified server-side bypass or account state change has been documented. Further testing should only occur with explicit authorization and within the permitted program scope.

## Disclosure

This report is intended for responsible disclosure through the target's authorized security reporting channel, if one is available.

Confirm the program's scope, permitted testing methods, evidence-handling requirements, and disclosure policy before submission. Do not publish confidential evidence or claim a confirmed vulnerability until the server-side impact has been established.

