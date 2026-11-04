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

1. Open TaskBear's sign-up page and register using an email address you control.
2. Submit an invalid OTP and capture the `401 Unauthorized` response using Burp Suite.
3. Repeat the process with a valid OTP and capture the successful `201 Created` response.
4. Compare both responses using Burp Suite and Meld, then modify the unsuccessful response to match the successful one.
5. Forward the modified response, complete registration, and check whether the dashboard is accessible without valid OTP verification.

## Proof of Concept (PoC)

### Screenshot 1: Sign-Up Page

**Description:** The application's sign-up page displaying the registration form.

<img width="736" height="710" alt="image" src="https://github.com/user-attachments/assets/b4538357-5483-4f1f-8eda-4e0f3ae75e14" />

### Screenshot 2: Valid OTP Verification Response

**Description:** Captured the server's response to a valid email OTP verification request using Burp Suite Proxy.

<img width="1917" height="1017" alt="image" src="https://github.com/user-attachments/assets/c274e9a4-112f-4228-8fe6-b1ab7ba7f99e" />

### Screenshot 3: OTP Verification Request with Invalid OTP

**Description:** The email OTP verification request captured using Burp Suite Proxy, showing the submitted email address and a fabricated OTP value.

<img width="1917" height="1016" alt="image" src="https://github.com/user-attachments/assets/8c3473c4-298f-456c-baf0-3ec082e5ad56" />

### Screenshot 4: Server Response to Invalid OTP

**Description:** The server response captured in Burp Suite after submitting a fabricated OTP (`123456`) to the email verification endpoint.

<img width="1917" height="1017" alt="image" src="https://github.com/user-attachments/assets/ac594719-0a7a-421a-9de4-02034846c160" />

### Screenshot 5: Manual Modification of OTP Verification Response

**Description:** Burp Suite response editor showing a manually modified HTTP response for an OTP verification request containing an invalid OTP.

<img width="1917" height="1016" alt="image" src="https://github.com/user-attachments/assets/f3c54456-6ee9-470e-86fa-6db6ffcd4c5d" />

### Screenshot 6: Registration Form After OTP Bypass Attempt

**Description:** The TaskBear account registration form populated with the required user details, including first name, last name, email address, mobile number, date of birth, and password.

<img width="683" height="897" alt="image" src="https://github.com/user-attachments/assets/f8ebb912-0845-4555-ad8e-29fc8535b102" />

### Screenshot 7: Successful Account Creation After OTP Bypass

**Description:** The TaskBear user dashboard displayed after submitting the registration form.

<img width="1777" height="892" alt="image" src="https://github.com/user-attachments/assets/933c73a5-df61-48a6-a240-52737b10a7de" />


## Impact

An attacker may potentially:

- Proceed through registration without completing valid email OTP verification.
- Create accounts with unverified email addresses if backend verification is not enforced.
- Undermine the application's email ownership verification controls.

## Remediation

1. **Server-Side Validation:** Verify OTPs on the backend before marking an email as verified.
2. **Secure OTP Handling:** Bind OTPs to the correct email and registration session, enforce expiration, and limit attempts.
3. **Registration Enforcement:** Prevent account activation or protected access until server-side email verification succeeds.
