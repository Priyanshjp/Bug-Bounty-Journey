# TaskBear OTP Verification

# Vulnerability Report

## Title

Potential OTP Verification Bypass via HTTP Response Manipulation in TaskBear Sign-Up Flow

## Summary

During testing of the email-based OTP verification mechanism, registration was attempted using a new email address. An invalid OTP was submitted, and the original HTTP response returned `401 Unauthorized`.

The response was modified locally in Burp Suite to use `201 Created` with a modified response body. After forwarding the modified response, the registration flow proceeded. The account was subsequently created, and successful login was reported.

This behaviour raises concerns about whether the application relies on client-side response handling to determine the outcome of OTP verification. The server-side email verification state has not been independently confirmed.

## Affected asset

- **Program:** TaskBear — authorization and program scope not confirmed.
- **In-scope hostname / app:** `https://taskbear.com.au/` — subject to scope confirmation.
- **Endpoint and HTTP method:** Sign-up and OTP verification flow; exact verification endpoint and HTTP method not recorded.
- **Affected role:** Unauthenticated user registering a new account.
- **Environment:** Web application tested using Burp Suite.

## Severity rationale

- **Suggested severity:** Pending confirmation; no final severity assigned.
- **Impact:** Account creation and successful login were reported after local HTTP response manipulation.
- **Exploitability / prerequisites:** A new email address, access to the registration flow, and local HTTP response manipulation using Burp Suite.
- **Program-specific severity guidance considered:** Not assessed. The applicable security testing policy and severity guidelines should be reviewed before submission.

## Preconditions

- Access to the TaskBear sign-up page.
- A new email address controlled by the tester.
- An OTP verification flow initiated through the application.
- Burp Suite configured to inspect HTTP traffic.
- Authorization to test the target within the applicable security testing scope.

## Steps to reproduce

1. Open the TaskBear sign-up page: `https://taskbear.com.au/auth/sign-up?redirect=%2F`.
2. Initiate registration using a new email address controlled by the tester.
3. Proceed to the email OTP verification step.
4. Submit an invalid OTP.
5. Intercept and inspect the HTTP response using Burp Suite.
6. Observe the original response status: `401 Unauthorized`.
7. Modify the response locally to use `201 Created` and change the response body.
8. Forward the modified response to the browser.
9. Observe that the registration flow proceeds.
10. Complete the registration process and observe that an account is created.
11. Attempt to log in using the newly created account and record the resulting behaviour.

**Reproduction note:** Account creation and successful login were reported during testing. Whether the server marked the email address as verified remains unconfirmed.

## Expected result

The application should reject an invalid OTP and prevent email verification from being completed.

The backend should independently validate the OTP and maintain the authoritative verification state. A locally modified HTTP response should not cause an account to be treated as verified without successful server-side validation.

## Actual result

The invalid OTP submission initially returned `401 Unauthorized`. After the response was modified locally in Burp Suite to use `201 Created` with a modified response body, the registration flow proceeded.

The account was reportedly created, and successful login was achieved. The server-side email verification state has not been independently confirmed.

## Security impact

Potential security implications include:

1. **Potential OTP verification bypass:** Local response manipulation changed the observed registration flow after an invalid OTP submission.
2. **Account registration:** An account was reportedly created after the response was modified.
3. **Successful login:** Login using the newly created account was reported.
4. **Email ownership verification concerns:** If the backend does not independently enforce OTP verification, users may be able to register without proving ownership of the supplied email address.
5. **Potential access-control implications:** The impact on protected functionality depends on whether the server considers the account verified and whether verified email is required for that functionality.

**Observed:** The original response was `401 Unauthorized`; after local response modification, registration proceeded, and account creation and successful login were reported.

**Not independently confirmed:** Server-side OTP validation bypass, verified-email status, or access to features restricted to verified accounts.

## Evidence

### Proof of Concept (PoC)

**Video Evidence Provided:** `evidence/otp-bypass-demo.mp4`

The provided video demonstrates the OTP registration flow, the original `401 Unauthorized` response following an invalid OTP submission, the locally modified HTTP response in Burp Suite, and the subsequent registration and login behaviour.

The video is supplied as supporting evidence of the observed behaviour. Server-side OTP verification bypass has not been independently confirmed.

## Remediation

1. Enforce OTP validation on the server before marking an email address as verified.
2. Never rely on client-side response handling to determine whether OTP verification succeeded.
3. Maintain the authoritative verification status on the server.
4. Create authenticated sessions only according to the application's server-side authentication and verification requirements.
5. Bind OTPs to the intended account and verification session.
6. Enforce OTP expiration and single-use restrictions.
7. Implement rate limiting and monitoring for repeated OTP verification failures.
8. Ensure protected API endpoints enforce the required account verification status.
9. Test invalid, expired, replayed, and manipulated OTP scenarios within an authorized test environment.

## Retest history

- **Date:** Not recorded.
- **Result:** Account creation and successful login were reported following local response manipulation.
- **Notes:** Server-side verification status remains unconfirmed. Further testing should only be conducted with explicit authorization and within the permitted scope.

## Disclosure

Submit this report through the target's authorized security reporting channel, if available. Confirm that the target is within the permitted testing scope and follow the program's confidentiality and disclosure requirements.

Do not publish confidential evidence or claim a confirmed backend vulnerability until the relevant server-side behaviour has been established.
