# OTP Verification Bypass — Lab Report

**Author:** Priyansh J Panchal  
**Learning Track:** Bug Bounty Journey — Day 01  
**Assessment Type:** Educational lab / authorized testing  
**Status:** Draft template — replace all bracketed fields with observations from your own lab.

> **Scope and safety:** This report is for a lab or an application where you have explicit authorization. Do not test OTP bypass on real users or production accounts without written permission. Never include real OTPs, phone numbers, session cookies, access tokens, or personal data in a public repository.

---

## 1. Summary

**Title:** OTP Verification Bypass in [feature / workflow]  
**Affected asset:** [Lab name or explicitly authorized hostname]  
**Affected endpoint:** `[HTTP method] [redacted endpoint path]`  
**Severity:** [Assess after validating impact and program criteria]  
**Weakness category:** Authentication / Improper Verification of OTP  
**CWE mapping:** Potentially CWE-287 (Improper Authentication); confirm the mapping fits the actual root cause.

### Executive summary

During testing in an authorized lab, I evaluated the OTP verification flow for [signup / login / password reset / phone change]. The test was intended to determine whether the server correctly enforced OTP verification before completing the protected action.

**Observed result:** [Describe exactly what happened. Do not claim a bypass unless the protected action succeeded without valid OTP verification.]

**Security impact:** If confirmed, this weakness may allow an attacker to complete [specific protected action] without successfully proving possession of the registered factor. The actual impact depends on the workflow, account protections, and what action becomes available.

---

## 2. Scope and Preconditions

- **Program / lab:** [Name]
- **In-scope asset:** [Authorized hostname or lab identifier]
- **Test date:** [YYYY-MM-DD]
- **Account used:** [Self-controlled test account; no personal details]
- **Environment:** [Lab / staging / authorized production program]
- **Relevant rules reviewed:** [Yes / No]
- **Preconditions:** [e.g., test account created; OTP flow initiated]

Do not publish confidential program details or sensitive evidence in a public repository.

---

## 3. Technical Details

### Expected secure behavior

The server should validate the OTP for the correct user, purpose, and active challenge before allowing the protected action. Verification should be enforced server-side and should expire appropriately.

### Actual behavior

[Write what you observed in the lab. State whether the server accepted an invalid, expired, reused, or absent OTP, or whether the issue was only a client-side UI behavior. Do not guess.]

### Root-cause hypothesis

[Only include a root-cause hypothesis supported by evidence. Possible areas to investigate include server-side verification enforcement, challenge-to-session binding, expiry, one-time use, and purpose binding.]

---

## 4. Reproduction Steps

Use only the lab or explicitly authorized target. Keep steps precise and reproducible.

1. Open [lab / authorized application] and sign in with a test account if required.
2. Navigate to [OTP-protected workflow].
3. Initiate the OTP challenge using test data.
4. Capture the relevant request in Burp Suite Proxy / HTTP history.
5. Send the request to Repeater for controlled inspection.
6. Perform the specific test demonstrated in the lab: [describe the test accurately, without real credentials or secrets].
7. Observe the server response and whether the protected action actually completed.
8. Repeat once in a clean test state, if safe and allowed, to confirm reproducibility.
9. Stop testing after confirming the behavior; do not access another user's account or data.

### Result

- **Expected:** [OTP verification must be required before the protected action.]
- **Observed:** [Exact response and application behavior.]
- **Reproducible:** [Yes / No / Not yet verified]
- **Evidence captured:** [Sanitized screenshot / request / response]

A success status code alone does not prove an OTP bypass. Confirm the protected action's result and record the relevant server-side behavior.

---

## 5. Evidence

Add sanitized screenshots in the `screenshots/` folder and reference them here.

- `screenshots/01-otp-flow.png` — [What this screenshot demonstrates]
- `screenshots/02-redacted-request.png` — [Relevant request with secrets removed]
- `screenshots/03-redacted-response.png` — [Response and confirmed outcome]

**Redaction checklist**
- [ ] OTP values removed
- [ ] Cookies and session IDs removed
- [ ] Authorization headers / bearer tokens removed
- [ ] Phone numbers, email addresses, and personal data removed
- [ ] Confidential program identifiers removed if disclosure is prohibited
- [ ] Only test-account data is shown

Never commit live secrets or raw Burp project files to a public repository.

---

## 6. Impact Assessment

Complete only what the evidence supports.

- **Security boundary affected:** [Authentication / account recovery / factor verification / other]
- **Attacker prerequisites:** [What access or interaction is required]
- **Protected action reached without valid verification:** [Specific action, if confirmed]
- **Accounts or data exposed:** [None / describe only controlled test data]
- **Business impact:** [Evidence-based explanation]
- **Limitations:** [What was not tested]

### Severity

**Proposed severity:** [Informational / Low / Medium / High / Critical]

**Rationale:** [Explain likelihood, prerequisites, and demonstrated impact. Follow the program's severity rubric; do not assign severity based solely on the phrase “OTP bypass.”]

---

## 7. Recommended Remediation

1. Enforce OTP verification on the server before the protected action is performed.
2. Bind each OTP challenge to the intended user, session, and purpose (e.g., login vs password reset).
3. Apply a short expiry and ensure each OTP can be used only once.
4. Apply rate limits and abuse monitoring to OTP generation and verification attempts.
5. Invalidate or supersede older challenges where appropriate.
6. Avoid relying on client-side flags, hidden fields, or UI state as proof of verification.
7. Log security-relevant failures without logging OTPs, passwords, or full session secrets.
8. Add regression tests for invalid, expired, reused, wrong-purpose, and wrong-account challenges.

---

## 8. Retest Checklist

- [ ] Correct OTP succeeds for the intended challenge.
- [ ] Incorrect OTP is rejected server-side.
- [ ] Expired OTP is rejected.
- [ ] Reused OTP is rejected.
- [ ] OTP from another account or challenge is rejected.
- [ ] OTP for one purpose cannot authorize a different purpose.
- [ ] Protected action remains blocked until verification succeeds.
- [ ] Rate limits behave according to the application's policy.

Only test cases permitted by the lab or program should be executed.

---

## 9. Conclusion

[Summarize whether the issue was confirmed, the exact security boundary affected, and the next action. If the lab exercise only demonstrated a theoretical or client-side behavior, say so clearly.]

**Learning notes:** This exercise reinforced that OTP checks must be enforced server-side and tied to the correct account, session, challenge, and purpose.

---

## 10. References

- [OWASP Authentication Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html)
- [OWASP Multifactor Authentication Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Multifactor_Authentication_Cheat_Sheet.html)
- [PortSwigger Web Security Academy](https://portswigger.net/web-security)

---

**Report author:** Priyansh J Panchal  
**Repository section:** `Day_01/OTP-Verification-Bypass/`
