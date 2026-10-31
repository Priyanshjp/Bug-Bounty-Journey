# Day 01 — Burp Suite Fundamentals & Web Authorization Testing

**Author:** Priyansh J Panchal  
**Focus:** Web Application Security • Burp Suite • HTTP • Session Management • Access Control

> A hands-on learning log covering the foundations of intercepting and understanding HTTP traffic, session handling, and authorization testing. Perform practical tests only on applications you own or environments explicitly authorized for security testing.

## Learning Objectives

- Understand Burp Suite's core tools and request/response workflow.
- Differentiate HTTP `GET` and `POST` requests.
- Understand cookies, bearer tokens, and session management.
- Configure FoxyProxy to route browser traffic through Burp Suite.
- Interpret common HTTP status codes.
- Understand how to test for broken access control using controlled test accounts.

## 1. Burp Suite Overview

Burp Suite is a web security testing platform used to inspect, modify, replay, and analyze HTTP/S traffic.

### Main components

| Component | Purpose |
|---|---|
| Proxy | Intercepts and inspects browser traffic |
| HTTP history | Keeps a record of requests and responses passing through the proxy |
| Repeater | Sends a request repeatedly with controlled changes |
| Intruder | Automates customized request variations; use only within program rules |
| Decoder | Encodes/decodes data formats |
| Comparer | Compares two requests or responses |
| Sequencer | Helps analyze randomness in tokens and session identifiers |
| Target / Site map | Organizes discovered hosts, paths, and application structure |
| Logger | Provides a broader view of tool traffic in supported editions/configurations |

### Basic workflow

1. Open Burp Suite and start a temporary project.
2. Open **Proxy → Intercept** and confirm the proxy listener is running (commonly `127.0.0.1:8080`).
3. Configure a dedicated browser profile to use the proxy.
4. Visit an authorized test application.
5. Review requests in **Proxy → HTTP history**.
6. Send a request to **Repeater** to inspect it and test one safe change at a time.
7. Save sanitized evidence; never commit session secrets to a public repository.

## 2. FoxyProxy Setup

FoxyProxy is a browser extension that makes it easy to switch between proxy configurations.

1. Install FoxyProxy from the browser's official extension store.
2. Add a proxy profile:
   - Type: HTTP
   - Host: `127.0.0.1`
   - Port: `8080` (or the listener port configured in Burp)
3. Enable the profile only when testing an authorized application.
4. For HTTPS inspection, configure Burp's CA certificate in a dedicated testing browser/profile, following PortSwigger's official documentation.
5. Disable the proxy when finished.

**Troubleshooting**
- Check that Burp is running and the listener is enabled.
- Confirm browser host and port match Burp's listener.
- Check that the browser is using the intended profile.
- If HTTPS shows certificate warnings, follow the official Burp CA setup process in the test profile. Do not weaken certificate validation globally.

## 3. HTTP Request Anatomy

An HTTP request generally contains a request line, headers, a blank line, and sometimes a body.

```http
GET /account/profile HTTP/1.1
Host: app.example.test
Accept: text/html
Cookie: session=REDACTED
```

Common parts:
- **Method:** action requested from the server, such as GET or POST.
- **Path:** resource being requested.
- **Headers:** metadata such as Host, Accept, Content-Type, Authorization, and Cookie.
- **Body:** optional data, often used with POST, PUT, or PATCH.

Use reserved `.test` example domains or a local training application in notes. Replace examples only with an authorized target during a permitted lab.

## 4. GET vs POST

| Property | GET | POST |
|---|---|---|
| Typical use | Retrieve a representation/resource | Submit data or request an operation |
| Parameters | Often in URL query string | Often in request body, but can also be in URL |
| Safe by HTTP semantics | Intended to be safe/read-only | Not generally safe |
| Idempotent by HTTP semantics | Yes, intended to be | Not guaranteed |
| Visibility | Query parameters may appear in browser history, logs, and URLs | Body is not normally in the URL, but is still visible to clients, proxies, and servers |
| Security note | Do not put secrets in query strings | POST does not automatically make data secure; HTTPS and proper controls still matter |

### Example GET

```http
GET /products?id=42 HTTP/1.1
Host: app.example.test
Accept: application/json
```

### Example POST

```http
POST /api/profile HTTP/1.1
Host: app.example.test
Content-Type: application/json
Cookie: session=REDACTED

{"displayName":"Test User"}
```

**Key point:** HTTP method alone does not enforce authorization. The server must validate the user's permissions for the requested action and object.

## 5. Cookies, Tokens, and Sessions

### Cookies
Cookies are browser-managed name/value data sent to matching sites according to cookie scope and policy. Session cookies commonly associate a browser with server-side session state.

Important cookie attributes:
- `Secure`: send cookie over HTTPS.
- `HttpOnly`: prevent ordinary JavaScript access to the cookie.
- `SameSite`: restrict some cross-site cookie sending.
- `Domain` and `Path`: constrain where a cookie is sent.
- `Expires` / `Max-Age`: affect cookie lifetime.

### Tokens
A token is a credential or claim-bearing value used to authenticate or authorize a request. Examples include bearer access tokens and signed tokens such as JWTs.

- Treat tokens like passwords.
- Never commit real tokens to GitHub.
- A JWT's payload is typically encoded, not encrypted by default.
- A token's presence does not prove that the server validates its signature, expiry, issuer, audience, or permissions correctly.

### Sessions
A session represents state associated with a user or client across requests. A common pattern is a random session identifier in a cookie, with session data stored server-side.

Checks to understand in an authorized lab:
- Is the session rotated after authentication or privilege changes?
- Does logout invalidate the server-side session?
- Does password reset or account recovery invalidate sessions when expected?
- Are cookies protected with appropriate attributes?
- Does the server enforce permissions on every sensitive request?

Do not copy real cookies or bearer tokens into screenshots, notes, issue reports, or commits. Redact values, for example `Cookie: session=[REDACTED]`.

## 6. HTTP Status Codes

| Code | Meaning | Typical interpretation |
|---|---|---|
| `200 OK` | Request succeeded | Check the response body and actual effect |
| `201 Created` | Resource created | Common after successful creation |
| `204 No Content` | Succeeded without response body | Common after updates/deletes |
| `301` / `302` | Redirect | Inspect `Location`; redirects do not themselves prove a vulnerability |
| `400 Bad Request` | Invalid request | Syntax/validation issue |
| `401 Unauthorized` | Authentication missing/invalid | Often needs valid authentication |
| `403 Forbidden` | Server refuses the request | Often authenticated but not allowed; implementations vary |
| `404 Not Found` | Resource not found or intentionally concealed | May be used to hide resource existence |
| `405 Method Not Allowed` | Method unsupported for resource | Check `Allow` header if present |
| `409 Conflict` | Request conflicts with current state | Can reflect state/duplicate-operation conflict |
| `415 Unsupported Media Type` | Content type unsupported | Check `Content-Type` |
| `429 Too Many Requests` | Rate limit triggered | Stop and follow the program's rules |
| `500 Internal Server Error` | Server-side error | Record minimal sanitized evidence; do not assume exploitable impact |

A status code alone does not confirm a vulnerability. Always assess the response body, headers, application state, role, and expected behavior.

## 7. Authorization / Access-Control Testing

Authorization decides what an authenticated or anonymous user is allowed to do. Authentication answers who the user is; authorization answers what that user may access or change.

### Safe lab setup
- Use an intentionally vulnerable training app or a program explicitly authorizing the target.
- Create two test accounts you control (Account A and Account B), where permitted.
- Create harmless test objects owned by each account.
- Never use other people's accounts or data.

### Manual workflow in Burp Repeater
1. Sign in as Account A and capture a normal request for an object owned by A.
2. Record the method, endpoint, object reference, and expected access policy.
3. Sign in as Account B in a separate test session and capture B's normal request.
4. In Repeater, use only your controlled test accounts and synthetic objects to compare the behavior.
5. Check whether the server enforces object ownership and role permissions on read, update, delete, export, and administrative actions.
6. Stop as soon as a boundary failure is demonstrated. Do not enumerate identifiers or access real user data.
7. Save minimal, redacted evidence and document expected vs actual behavior.

### What to look for
- A user can access another test user's object without permission.
- A lower-privilege role can invoke a privileged function.
- The UI hides an action but the server fails to enforce the permission.
- A user can modify protected fields that should be server-controlled.
- Tenant/workspace boundaries are not enforced server-side.

### Evidence template
- **Title:**
- **Authorized asset:**
- **Test accounts / roles:**
- **Preconditions:**
- **Steps to reproduce:**
- **Expected result:**
- **Actual result:**
- **Sanitized request/response:**
- **Demonstrated impact:**
- **Remediation:**

Recommended remediation generally includes server-side access checks for every relevant object/action and deny-by-default authorization.

## 8. Practice Checklist

- [ ] Started Burp Suite and verified proxy listener.
- [ ] Configured FoxyProxy for a dedicated test browser.
- [ ] Captured an HTTP request in Proxy history.
- [ ] Identified request line, headers, and body.
- [ ] Compared GET and POST examples.
- [ ] Identified cookie and token locations without exposing secrets.
- [ ] Reviewed session lifecycle and cookie attributes.
- [ ] Identified common HTTP status codes.
- [ ] Practiced Repeater using a controlled lab request.
- [ ] Reviewed authorization using only test accounts and synthetic data.
- [ ] Saved sanitized notes and screenshots.
- [ ] Confirmed no secrets or private program data are included in this repository.

## 9. Key Takeaways

- Burp Suite helps inspect and replay HTTP traffic; it does not automatically prove a security issue.
- GET and POST are different HTTP methods, not security controls.
- Cookies, tokens, and sessions are sensitive credentials/state and must be handled carefully.
- HTTP status codes need context; a `200`, `403`, or `404` alone is not a vulnerability.
- Broken access control is confirmed by demonstrating that the server permits an unauthorized action on a controlled test object.
- Clear, minimal, reproducible evidence makes a stronger report.

## 10. Official Learning Resources

- [PortSwigger Web Security Academy](https://portswigger.net/web-security)
- [Burp Suite documentation](https://portswigger.net/burp/documentation)
- [HTTP Semantics — RFC 9110](https://www.rfc-editor.org/rfc/rfc9110)
- [MDN: HTTP request methods](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods)
- [MDN: HTTP response status codes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)
- [OWASP Web Security Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)

---

**Day 01 complete:** Burp Suite overview, FoxyProxy, HTTP methods, cookies, tokens, sessions, status codes, and controlled authorization testing.
